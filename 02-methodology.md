# 02 — Metodología: Spec-Driven Development

## La idea central

Nada se implementa sin una especificación que lo respalde.

No es waterfall. No es "escribir documentos que nadie lee." Es un ciclo corto:

```
Idea → Spec (requirements + design + tasks) → Implementar → Tests → Verificar → Ship
```

La diferencia con "simplemente codificar" es que el spec actúa como contrato entre el humano que decide y el AI que implementa.

## Anatomía de una Kiro Spec

Cada módulo tiene tres documentos en `.kiro/specs/<módulo>/`:

### 1. `requirements.md` — Qué construir

- User stories con acceptance criteria
- Formato EARS (WHEN/IF/THEN/SHALL)
- Cada criterio es testeable — si no se puede verificar, no está listo

### 2. `design.md` — Cómo construirlo

- Entidades, modelos de datos, diagramas
- API contracts
- **Correctness Properties** — invariantes formales del sistema

Ejemplo real del módulo de Reviews:
```
Property 3: Shared Review Access Control
A shared review is visible only to active members of the target 
(group or club) at query time.
Validates: Requirement 1.5
```

### 3. `tasks.md` — Plan de implementación

- Tasks ordenados con dependencias (DAG)
- Agrupados en "waves" — tasks dentro de la misma wave son paralelizables
- Cada task referencia los requirements y properties que cubre

## El workflow completo

```
┌─────────────────────────────────────────────────────┐
│  1. Leer PROJECT_CONTEXT.md + ADRs relevantes       │
│  2. Cargar la spec del módulo                        │
│  3. Detectar ambigüedades → preguntar si hay dudas  │
│  4. Implementar task por task, wave por wave         │
│  5. Tests para cada correctness property             │
│  6. Verificar Definition of Done                     │
│  7. Proponer commit (nunca automático)               │
│  8. Esperar aprobación humana                        │
└─────────────────────────────────────────────────────┘
```

## Definition of Done

Una task está completa sólo si:

- ✓ Requisitos implementados según la spec
- ✓ Tests pasando (domain + integration)
- ✓ Documentación sincronizada
- ✓ Arquitectura preservada (no viola ningún ADR)
- ✓ Sin ambigüedades sin resolver
- ✓ Lista para review

## Conventional Commits

Cada commit sigue un formato estricto:
```
<tipo>(<scope>): <descripción corta>

Cuerpo:
- Resumen de implementación
- Decisiones de diseño relevantes

References:
- ADR: 0001, 0004
- Spec: authentication
- Tasks: authentication/tasks.md#1, #2
```

## Branching

```
feat/<milestone>-<descripción>
```

Ejemplos reales del proyecto:
- `feat/m1-auth-registration-login-refresh`
- `feat/m6.5-frontend-catchup`
- `feat/m9-release-candidate`

Una branch por PR. Un PR por unidad lógica de trabajo. Después de merge, branch nueva desde main actualizado.

## Por qué funciona

1. **Reduce re-trabajo** — cuando la spec está clara, la implementación es casi mecánica.
2. **Facilita el AI pairing** — el agente no tiene que adivinar la intención; la spec es explícita.
3. **Crea trazabilidad** — de requirement a test, pasando por design y task.
4. **Previene scope creep** — si no está en la spec, no se implementa.
5. **Documenta el "por qué"** — no sólo qué se hizo, sino por qué se diseñó así.

## Specs como producto vivo

Las specs no son estáticas. Cuando la implementación revela un caso no contemplado:

1. Se detiene la implementación
2. Se propone el cambio a la spec
3. Se espera aprobación
4. Se actualiza la spec
5. Se continúa

Nunca se implementa comportamiento no documentado de forma silenciosa.
