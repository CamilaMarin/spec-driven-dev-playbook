# 01 — Foundations: Decisiones Antes de Escribir Código

## El problema real

Antes de abrir un editor, el proyecto partió de un problema concreto: una familia con cientos de libros físicos y digitales, sin forma simple de saber qué tienen, elegir qué leer juntos, ni organizar un club casero. Las soluciones existentes (Goodreads, StoryGraph, Calibre, Komga, Kavita) cubren partes del problema — ninguna las integra de forma simple y privada.

## Principios de producto como ancla

Antes de elegir tecnología, definí 7 principios de producto que funcionan como test de decisión para todo lo que viene después:

1. **Las personas están primero que los libros.** El objetivo es fomentar experiencias compartidas, no acumular metadata.
2. **El usuario es dueño de su biblioteca y sus datos.** Puede exportarlos o borrarlos en cualquier momento.
3. **Privacy by Design** — Ley 21.719 desde el primer sprint.
4. **La app acompaña la lectura, no la reemplaza** — sin mecánicas de red social.
5. **Todo debe sentirse cercano y sencillo.**
6. **Cloud-agnostic y costo cero por defecto.**
7. **Mobile-first.**

Cada vez que una feature se sentía ambigua, la respuesta estaba en alguno de estos principios.

## ADRs: las decisiones que definen el proyecto

El arma más importante del proyecto son los **Architecture Decision Records (ADRs)**. 18 decisiones documentadas antes y durante el desarrollo, cada una con contexto, decisión, y consecuencias.

### Las decisiones fundacionales (antes de codificar)

| ADR | Decisión | Impacto |
|-----|----------|---------|
| 0001 | No compartir archivos digitales entre cuentas | Elimina el riesgo legal de distribución de copyright. Alinea con *Hachette v. Internet Archive* (2024). |
| 0002 | Stack: FastAPI + Next.js + PostgreSQL, zero-cost-first | Docker local → Supabase free → Render/Vercel → Cloud sólo si escala. |
| 0003 | Cumplir Ley 21.719 desde diseño | ARCO self-service, consent-gating, auditoría, retención configurable — no como deuda técnica. |
| 0004 | JWT custom (Access + Refresh Token) | Sin dependencia de Supabase Auth ni Cognito. Stateless, portable, testeable. |
| 0009 | Aislamiento estricto de archivos digitales | El reader sólo abre archivos del usuario autenticado. La "biblioteca compartida" es metadata. |
| 0015 | Separación Book / Copy | Book = obra intelectual. Copy = instancia con dueño. Loans ↔ Copy, no Book. |
| 0017 | Todo detrás de abstracciones | FileStorage, TokenService, MetadataProvider — cloud agnostic real. |

### Las decisiones de scope (qué NO hacer)

| ADR | Eliminado del MVP | Razón |
|-----|-------------------|-------|
| 0005 | Cuentas de menores | Complejidad de permisos → diferido a v2. |
| 0006 | Grupos conectados | Sin entidad definida, permisos inmanejables → eliminado. |
| 0012 | Redis | JWT no necesita estado server-side → innecesario. |
| 0018 | Encriptación a nivel de aplicación | Key management complejo; encriptación de infraestructura es suficiente pre-launch. |

Decir "no" explícitamente es tan valioso como decir "sí." Cada ADR de exclusión evitó semanas de scope creep.

## Por qué ADRs y no "ya lo decido después"

Los ADRs cumplen tres funciones simultáneas:

1. **Documentación para el yo futuro** — cuando en la sesión 15 ya no recuerdo por qué no usé Redis.
2. **Guardrails para el AI** — las steering rules hacen que el agente nunca viole una decisión aprobada.
3. **Portfolio piece** — demuestran pensamiento arquitectónico, no sólo código.

Un ADR no es burocracia. Es un contrato consigo mismo que evita rehacer la misma discusión tres veces.

## Resultado de esta fase

Al terminar las foundations, antes de escribir una línea de lógica de negocio, tenía:

- 7 principios de producto
- 18 ADRs documentados
- Stack definido y justificado
- Scope explícitamente acotado (qué sí, qué no, qué después)
- Un `PROJECT_CONTEXT.md` como punto de entrada único

Todo esto funciona como "constitución" del proyecto. Cada milestone posterior es una implementación que no contradice estos cimientos.
