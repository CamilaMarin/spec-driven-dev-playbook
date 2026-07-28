# 05 — AI Collaboration: Humano Decide, AI Implementa

## El modelo de trabajo

Este proyecto no fue "generado por AI." Fue **diseñado por un humano y ejecutado con AI como partner de implementación**.

La distinción importa:

| Responsabilidad | Humano | AI |
|-----------------|--------|-----|
| Visión de producto | ✓ | — |
| Decisiones de arquitectura | ✓ | propone opciones |
| ADRs | ✓ aprueba | ✓ redacta borrador |
| Specs (requirements) | ✓ valida | ✓ genera propuesta |
| Specs (design) | ✓ revisa properties | ✓ diseña |
| Implementación | ✓ revisa | ✓ codifica |
| Tests | ✓ define qué testear | ✓ escribe tests |
| Commits | ✓ aprueba | ✓ propone mensaje |
| Code review | ✓ final decision | ✓ pre-review |

## Las steering rules como contrato

El AI no trabaja "libre." Tiene un set de 22 reglas permanentes (steering rules) que definen:

- Qué puede hacer sin preguntar (leer archivos, proponer código)
- Qué requiere aprobación (commits, cambios de scope, nuevas dependencias)
- Qué está prohibido siempre (violar ADRs, file sharing, commits automáticos)

Esto convierte al AI en un implementador disciplinado, no en un agente autónomo que toma decisiones de producto.

## Patrones que funcionaron

### 1. Context loading explícito

Antes de cada tarea, el AI carga:
```
PROJECT_CONTEXT.md → ADRs del módulo → Spec completa → Steering rules
```

Esto elimina el "drift" donde el AI pierde contexto entre sesiones. El AI siempre parte del mismo baseline documental.

### 2. Spec como contrato de implementación

El AI no adivina qué construir. La spec define:
- Exactamente qué endpoints crear
- Qué invariantes respetar (correctness properties)
- Qué tests escribir
- Qué errores retornar

La conversación se reduce a: "implementa task 3 de la spec de loans." No hay ambigüedad.

### 3. Approval gates

```
[AI genera código]
   ↓
[AI propone commit con resumen + files + referencias]
   ↓
[Humano revisa]
   ↓ aprueba / pide cambios
[commit]
```

Nunca un commit automático. Nunca un push sin revisión. El humano siempre tiene la última palabra.

### 4. Incremental work, not monolithic drops

Cada task produce un delta pequeño y reviewable:
- 1-3 archivos nuevos
- Tests específicos para lo implementado
- Documentación actualizada

El AI propone, el humano revisa, se avanza. No "genérame todo el módulo de una vez."

### 5. Questions before assumptions

Si la spec tiene un caso no contemplado, el AI pregunta antes de decidir:

> "La spec de Reviews no define qué pasa si el target_id de un shared review es un club al que el autor no pertenece. ¿Debería retornar 403 o 422?"

La decisión la toma el humano. El AI no asume.

## Anti-patrones que evité

### ❌ "Hazme toda la app"

Nunca. Cada interacción es una task acotada con entrada y salida definidas.

### ❌ AI decide arquitectura

El AI propone opciones con trade-offs. El humano decide y eso se convierte en ADR.

### ❌ Copy-paste sin entender

Cada pieza de código generada fue revisada, entendida, y a veces modificada. El AI es un acelerador, no un reemplazo del entendimiento.

### ❌ Confiar sin tests

Incluso con Clean Architecture, el AI puede generar bugs sutiles. Los 379 tests son la red de seguridad real.

## El rol de Kiro como IDE

Kiro aporta capacidades específicas que potencian este modelo:

- **Specs estructuradas** — requirements + design + tasks como archivos formales, no conversación libre
- **Steering rules** — contexto permanente que el agente siempre carga
- **Task execution** — el agente trabaja task por task, no "a ver qué sale"
- **Hooks** — automatización post-commit (lint, test)

## Resultados del modelo

- **12 milestones completados** sin rehacer trabajo por malentendidos
- **379 tests** generados correctamente (con revisión humana)
- **0 commits revertidos** por errores de implementación
- **18 ADRs** que el AI nunca violó gracias a las steering rules
- **~5,000 LOC Python + ~6,500 LOC TypeScript** en una semana

## Lo que aprendí sobre AI pairing

1. **El AI es tan bueno como el contexto que le das.** Sin spec clara, genera basura.
2. **Las reglas importan más que los prompts.** Una steering rule permanente vale más que repetir instrucciones.
3. **Revisión humana no es opcional.** Es el quality gate real.
4. **La documentación es el multiplicador.** Cada ADR y spec ahorra conversaciones futuras.
5. **El humano debe entender todo lo generado.** Si no lo entiendes, no lo mergees.
