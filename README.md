# Spec-Driven Development Playbook

Un playbook detallado documentando cómo construí **EntreLíneas** — una plataforma web full-stack (FastAPI + Next.js) — desde la idea hasta Release Candidate usando spec-driven development, Clean Architecture, y AI como partner de implementación.

## Contenido

| Capítulo | Tema | Takeaway clave |
|----------|------|----------------|
| [00 — Overview](00-overview.md) | Resumen del proyecto y métricas | 12 milestones, 43 endpoints, 379 tests en un MVP |
| [01 — Foundations](01-foundations.md) | Decisiones iniciales | ADRs y principios de producto definen el "por qué" antes de codificar |
| [02 — Methodology](02-methodology.md) | Workflow y proceso | Specs → Tasks → Implement → Verify → Ship |
| [03 — Architecture](03-architecture.md) | Clean Architecture en práctica | El dominio nunca importa framework — de verdad |
| [04 — Milestones](04-milestones.md) | 12 entregas incrementales | Ordenamiento risk-first, cada milestone es deployable |
| [05 — AI Collaboration](05-ai-collaboration.md) | Trabajar con AI como dev partner | Humano decide, AI implementa, humano valida |
| [06 — Design System](06-design-system.md) | De genérico a identidad propia | Cómo "sala de lectura" se convirtió en un design system real |
| [07 — Lessons Learned](07-lessons-learned.md) | Retrospectiva | Qué funcionó, qué cambiaría |
| [08 — Results](08-results.md) | Números finales y producto | El resultado completo |

## El Proyecto

**EntreLíneas** es una plataforma web para gestionar bibliotecas personales (físicas y digitales) y fomentar la lectura compartida entre familias y clubes de lectura.

- 🔗 [Repositorio](https://github.com/CamilaMarin/library-platform)
- 🏷️ [Release v1.0.0-rc.1](https://github.com/CamilaMarin/library-platform/releases/tag/v1.0.0-rc.1)

## Para quién es esto

- Desarrolladores que buscan un approach estructurado para construir MVPs full-stack
- Cualquiera interesado en desarrollo de software asistido por AI de forma efectiva
- Equipos buscando un workflow spec-driven que puedan adoptar

## Stack

- **Backend:** Python, FastAPI, SQLAlchemy, Alembic, PostgreSQL
- **Frontend:** Next.js 16, React 19, TypeScript, Tailwind
- **Infraestructura:** Docker, MinIO, GitHub Actions
- **Metodología:** Clean Architecture, DDD (lightweight), ADRs, Kiro Specs

---

*Construido por [Camila Marín](https://github.com/CamilaMarin) — Julio 2026*
