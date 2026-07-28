# 04 — Milestones: 12 Entregas en un MVP

## Filosofía de los milestones

Cada milestone es **deployable por separado**. No es "phase 1 en 3 meses." Es una unidad de trabajo que deja el proyecto en estado funcional al completarse.

El orden no es cronológico por módulo — es **risk-first**: primero lo que podría fallar y bloquear todo, después lo que depende de eso, al final lo estético.

## El mapa completo

```
M-1  Architecture Validation      ← ¿funciona el stack end-to-end?
 M0  Privacy Foundation           ← legal desde el día 0
 M1  Authentication               ← sin auth no hay nada
 M2  Users & Groups               ← sin grupos no hay comunidad
 M3  Library                      ← el core del producto
 M4  Reading Selection            ← feature diferenciadora
 M5  Clubs                        ← social layer
 M6  Reviews                      ← opiniones, visibilidad
M6.5 Frontend Catchup             ← toda la UI de una vez
 M7  Loans                        ← préstamos físicos
 M8  Privacy Panel                ← ARCO completo
 M9  Release Candidate            ← polish, deployment, shelf view
```

## Por qué este orden

### M-1: Validación de Arquitectura

**Riesgo eliminado:** "¿funciona Docker + PostgreSQL + FastAPI + Next.js + MinIO en mi máquina?"

Antes de escribir lógica de negocio, confirmé que el stack se levanta, se conecta, y ejecuta una migración. 4 tests, lint clean, health endpoint E2E.

Si esto fallaba, todo lo demás era irrelevante.

### M0: Privacy Foundation

**Riesgo eliminado:** "¿la Ley 21.719 se puede integrar sin rediseñar el modelo de datos?"

DataConsent, AuditLog, DataProcessingRecord — las tablas y entidades que todo lo demás necesita. El consent-gating del registro se diseñó aquí.

### M1: Authentication (10 tasks)

**Riesgo eliminado:** "¿JWT custom sin Redis funciona para refresh token rotation?"

El módulo más grande en tasks. Incluye: registro con consent-gating, login, refresh con rotación atómica, logout, ARCO export y delete, tests de acceptance completos.

**154 tests** sólo en este módulo — la base de seguridad del proyecto.

### M2: Users & Groups (4 tasks)

Family groups con invitación, aceptación, y membership status. Relativamente simple gracias a que auth ya existe.

### M3: Library (7 tasks)

Book/Copy separation, CRUD, file isolation, search dentro de la biblioteca personal + grupo. La feature "Book puede existir sin Copy" se agregó más tarde en M9.

### M4: Reading Selection (5 tasks)

Sorteo aleatorio filtrable + pick-by-turn con rotación justa. El algoritmo cruza Books × Copies × Participants × Availability.

### M5: Clubs (5 tasks)

Clubes dentro de un grupo familiar. Reading turns, comments con spoiler toggle, active book management.

### M6: Reviews (6 tasks)

85 tests. El módulo con más tests por LOC, porque la visibilidad es compleja:
- `private` = sólo el autor
- `shared` = requiere target explícito (grupo o club) + el autor
- Query-time membership check — si te sales del grupo, pierdes acceso inmediatamente

### M6.5: Frontend Catchup (24 tasks)

Todo el frontend de una vez: 11 páginas, API client con retry, auth context, toast system, navigation, testing infra. La decisión de hacer "frontend en bloque" fue deliberada — el backend estaba completo y estable.

### M7: Loans (5 tasks)

Préstamos de copias físicas. El invariante más importante: **nunca un file_ref en un endpoint de loans**. 4 tests de regresión específicos para esto.

### M8: Privacy Panel (10 tasks)

ARCO completo como self-service: export, rectificación, oposición, cancelación con purge cross-cutting. FieldEncryptor, RetentionJob, breach playbook.

### M9: Release Candidate (10 tasks)

Reading Status + Shelf View, BookDetailModal, progress tracking, deployment (Docker prod + CI), UX polish final.

## Métricas por milestone

| Milestone | Tasks | Tests | Endpoints |
|-----------|-------|-------|-----------|
| M-1 | 7 | 4 | 1 |
| M0 | 3 | 10 | 0 |
| M1 | 10 | 154 | 4 |
| M2 | 4 | — | 3 |
| M3 | 7 | 26 | 7 |
| M4 | 5 | 21 | 3 |
| M5 | 5 | 17 | 8 |
| M6 | 6 | 85 | 4 |
| M6.5 | 24 | 24 | — |
| M7 | 5 | 16 | 4 |
| M8 | 10 | 47 | 4 |
| M9 | 10 | — | 5+ |

## Lo que se difirió (y por qué está bien)

| Feature | Razón del defer |
|---------|----------------|
| Integrated Reader (EPUB/PDF) | Mayor riesgo técnico; reader libraries (epub.js, PDF.js) merecen su propio milestone en v1 |
| Metadata Import (Open Library) | Convenience feature, no bloquea nada |
| Application-level encryption | Key management complejo; infra-level encryption suficiente pre-launch |
| Automated retention worker | Operacional, no de dominio — el job manual existe y funciona |

Diferir no es abandonar. Es priorizar lo que aporta valor ahora y mantener lo otro documentado para cuando corresponda.
