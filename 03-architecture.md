# 03 — Architecture: Clean Architecture en la Práctica

## La promesa

"El dominio nunca importa framework o infraestructura."

Suena simple. Implementarlo de verdad, en un proyecto full-stack con auth, storage, privacy, y múltiples bounded contexts — eso es lo que diferencia este proyecto.

## La estructura real

```
app/
  identity/            # User, DataConsent, RefreshToken, FamilyGroup
    domain/            ← entidades puras, sin imports externos
    application/       ← use cases + protocols (interfaces)
    infrastructure/    ← SQLAlchemy models, repositorios concretos
    interface/         ← routers FastAPI, schemas Pydantic
  library/
    domain/
    application/
    infrastructure/
    interface/
  community/           # Club, ReadingTurn, Comment
  circulation/         # Loan (sólo físico)
  reviews/             # Review con visibilidad explícita
  reading_selection/   # Draw, TurnHistory
```

Cada contexto tiene las 4 capas. No hay shortcuts.

## Dependency Rule en acción

```
Interface → Application → Domain ← Infrastructure
```

La flecha invertida en Infrastructure es clave: la infra implementa interfaces definidas por la application layer, no al revés.

### Ejemplo concreto: préstamos

```python
# domain/entities.py — CERO imports de framework
@dataclass
class Loan:
    copy_id: UUID
    lender_id: UUID
    borrower_id: UUID
    status: LoanStatus

    @classmethod
    def create(cls, copy_type: str, **kwargs) -> "Loan":
        if copy_type != "physical":
            raise ValueError("Loans only for physical copies")
        return cls(status=LoanStatus.ACTIVE, **kwargs)

# application/protocols.py — interfaz, no implementación
class LoanRepository(Protocol):
    def save(self, loan: Loan) -> None: ...
    def find_active_by_copy(self, copy_id: UUID) -> Loan | None: ...

# application/register_loan.py — use case
class RegisterLoan:
    def __init__(self, loans: LoanRepository, copies: CopyQuery):
        self.loans = loans
        self.copies = copies

    def execute(self, request: RegisterLoanRequest) -> Loan:
        copy = self.copies.get_by_id(request.copy_id)
        if not copy:
            raise CopyNotFound()
        existing = self.loans.find_active_by_copy(request.copy_id)
        if existing:
            raise CopyAlreadyOnLoan()
        loan = Loan.create(copy_type=copy.format, ...)
        self.loans.save(loan)
        return loan

# infrastructure/repositories.py — implementación concreta
class SqlLoanRepository:
    def __init__(self, session: Session):
        self.session = session

    def save(self, loan: Loan) -> None:
        model = LoanModel.from_domain(loan)
        self.session.add(model)
        self.session.commit()
```

El dominio no sabe que existe PostgreSQL. El use case no sabe que existe SQLAlchemy. Cada capa hace exactamente su trabajo.

## Bounded Contexts y cómo se comunican

Los 6 contextos comparten **identificadores** (user_id, book_id, copy_id) pero nunca modelos internos.

```
Identity ──── user_id ────→ Library
                             ↓ book_id, copy_id
Community ←── book_id ──── Library
                             ↓ copy_id
Circulation ←── copy_id ── Library
```

Cuando un contexto necesita información de otro (ej: Clubs necesita saber si un usuario tiene copia de un libro), usa un **Query Protocol** que sólo expone lo mínimo:

```python
class CopyOwnershipQuery(Protocol):
    """Cross-context read — never returns file_ref."""
    def user_owns_copy_of_book(self, user_id: UUID, book_id: UUID) -> bool: ...
```

Nunca file_ref. Nunca el modelo completo del otro contexto.

## Abstracciones cloud-agnostic (ADR-0017)

| Abstracción | Implementación actual | Podría ser |
|-------------|----------------------|-----------|
| `FileStorage` | LocalFileStorage (filesystem) | S3, GCS, Supabase Storage |
| `TokenService` | PyJWT | Cualquier implementación JWT |
| `MetadataProvider` | (deferred) | Open Library, Google Books |
| Repositories | SQLAlchemy/PostgreSQL | Cualquier SQL engine |

Cambiar de MinIO a S3: un archivo de infraestructura. Cero cambios en domain o application.

## Lo que la arquitectura previene

| Violación | Qué impide |
|-----------|------------|
| Controller accede a la DB directamente | Bugs no testeables, acoplamiento |
| Domain importa SQLAlchemy | Imposible testear sin DB |
| Use case instancia su propia infra | No se puede inyectar un test double |
| Contexto A importa modelos de contexto B | Acoplamiento entre módulos |
| file_ref cruza un bounded context | Filtración de archivos privados |

## Testing: la prueba de que funciona

Gracias a Clean Architecture, los tests de dominio son **triviales de escribir**:

```python
def test_loan_rejects_digital_copy():
    with pytest.raises(ValueError, match="physical"):
        Loan.create(copy_type="digital", ...)
```

Sin mocks. Sin setup de DB. Sin fixtures complejas. El dominio es puro.

Los tests de integración usan la DB real (Docker), pero el domain layer nunca lo necesita.

## Números que demuestran la separación

| Capa | Tests sin mocks | Tests con DB |
|------|-----------------|-------------|
| Domain | 100% (puro) | 0% |
| Application | ~90% (protocols) | ~10% |
| Integration | 0% | 100% |

**379 tests totales** con esta distribución — la mayoría son rápidos porque no tocan infraestructura.
