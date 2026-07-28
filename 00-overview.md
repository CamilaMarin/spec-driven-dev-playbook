# 00 — Overview

## El Proyecto

**EntreLíneas** ("Entre Líneas") es una plataforma web para gestionar bibliotecas personales (físicas y digitales) y fomentar la lectura compartida entre familias y clubes de lectura.

No es una red social para lectores, no es un repositorio compartido de archivos, y no es un catálogo público. Es una herramienta privada, familiar, donde las historias acercan a las personas.

## En números

| Métrica | Valor |
|---------|-------|
| Milestones | 12/12 completados |
| Tests backend | 355 pasando |
| Tests frontend | 24 pasando |
| Endpoints REST | 43 |
| Migraciones Alembic | 12 |
| Architecture Decision Records | 18 |
| Kiro Specifications | 11 |
| Líneas de Python (backend) | ~5,000 |
| Líneas de TypeScript (frontend) | ~6,500 |
| Entidades de dominio | 12 |
| Use cases | 25+ |
| Componentes | 15 shared + 11 páginas |

## Lo que se construyó

### Módulos funcionales
1. **Authentication** — JWT custom (access + refresh tokens), sin auth de terceros
2. **Family Groups** — Membresía por invitación
3. **Library** — Books + Copies con file isolation, búsqueda, reading status
4. **Reading Selection** — Sorteo aleatorio + pick-by-turn
5. **Clubs** — Clubes de lectura con reading turns, comentarios con spoiler toggle
6. **Reviews** — Privadas/compartidas con visibilidad explícita
7. **Loans** — Préstamos físicos con vista de prestatario
8. **Privacy Panel** — Derechos ARCO completos (Ley 21.719 Chile)

### Infraestructura técnica
- Clean Architecture en todos los módulos
- Design system visual ("Sala de lectura")
- Docker production deployment
- CI/CD pipeline (lint + test + build)
- 18 ADRs documentando cada decisión significativa

## Timeline

El proyecto se construyó en **una semana**, con un día y medio dedicado exclusivamente a foundations (ADRs, principios de producto, specs) antes de escribir la primera línea de código.

## Diferenciadores clave

1. **Privacidad desde el día 1** — No agregada después como deuda de compliance
2. **Sin scope creep** — ADRs excluyen features explícitamente (Redis, cuentas de menores, file sharing)
3. **Spec-driven** — Nada implementado sin una especificación que lo respalde
4. **Cloud agnostic** — Toda dependencia de infraestructura detrás de una abstracción
5. **AI-assisted, human-directed** — AI implementa, humano decide la arquitectura
