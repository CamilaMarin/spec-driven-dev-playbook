# 07 — Lessons Learned: Retrospectiva

## Lo que funcionó

### 1. ADRs como constitución

Cada vez que surgía una duda ("¿usamos Redis?", "¿los menores?", "¿compartimos archivos entre cuentas?"), la respuesta ya estaba documentada. No hubo debates repetidos ni decisiones contradictorias.

**Takeaway:** Invertir tiempo en documentar decisiones antes de codificar ahorra más tiempo del que consume.

### 2. Specs antes de código

Cero veces tuve que rehacer un módulo entero porque "no era lo que quería." La spec define exactamente qué se construye — no hay sorpresas al final.

**Takeaway:** Una spec de 2 páginas evita una semana de refactoring.

### 3. Privacy desde el día 0

DataConsent, AuditLog, y consent-gating en el registro fueron M0 — antes de auth siquiera. Cuando llegó M8 (Privacy Panel), la base ya existía. ARCO fue "conectar los cables," no "redesignar el modelo de datos."

**Takeaway:** La compliance no es un layer que se agrega al final. Es una decisión de data model que permea todo.

### 4. Risk-first milestone ordering

Empezar con architecture validation (M-1) eliminó la duda existencial de "¿mi stack funciona?" antes de invertir en lógica de negocio. Si Docker + PostgreSQL + FastAPI no se conectaban, mejor saberlo el día 1.

**Takeaway:** Ataca primero lo que puede matarte.

### 5. Frontend en bloque (M6.5)

Hacer todo el backend primero (M1-M6) y luego todo el frontend de una vez fue controversial, pero funcionó:
- APIs estables = no hay chasing de endpoints que cambian
- Una sola sesión de diseño visual = coherencia
- Testing infra configurada una vez, usada 24 veces

**Takeaway:** No siempre "vertical slices" es mejor. A veces el bloque horizontal reduce fricción.

### 6. Correctness properties como guía de testing

En vez de "qué debería testear?", la spec dice: "Property 3: un review shared sólo es visible para miembros activos del target." El test es obvio. 85 tests en Reviews vinieron naturalmente de las 5 properties.

**Takeaway:** Define propiedades formales → los tests se escriben solos.

## Lo que haría diferente

### 1. Empezar con una página de frontend antes

Esperé hasta M6.5 para hacer el frontend. Hubiera sido útil tener al menos una landing o login funcional desde M1 para visualizar el progreso y testear UX assumptions early.

### 2. Más property-based testing con fast-check

Usé fast-check sólo en 5 properties del frontend. El backend hubiera beneficiado de PBT para cosas como:
- Token rotation bajo concurrencia
- Draw algorithm fairness
- Visibility rules exhaustivas

### 3. Trackear horas por milestone

Sé que el proyecto tomó una semana total (día y medio de foundations + cinco días y medio de implementación), pero no trackeé las horas de cada milestone individual. Para futuros proyectos, esos datos granulares serían valiosos.

### 4. Diseño visual antes del catchup

El design system "sala de lectura" se definió durante M6.5. Si lo hubiera pensado antes, el frontend no habría necesitado una migración de tokens — habría nacido con identidad.

### 5. Automated E2E tests

Los tests son domain + integration (backend) y unit + PBT (frontend). No hay Cypress/Playwright para E2E cross-stack. En retrospectiva, al menos un happy-path E2E por módulo habría dado más confianza.

## Decisiones que parecían arriesgadas y salieron bien

| Decisión | Riesgo percibido | Resultado |
|----------|------------------|-----------|
| JWT custom sin Redis | "¿y si necesito revocar tokens?" | Rotación atómica + SHA-256 storage = suficiente |
| No usar Supabase Auth | "es más fácil" | Mayor control, testing más simple, zero vendor lock |
| Clean Architecture para un MVP | "over-engineering" | Tests triviales, cambios de infra indoloros |
| 18 ADRs para un proyecto personal | "burocracia" | Cero decisiones contradictorias, AI nunca se desvía |
| Privacy como M0 | "nadie cumple eso en un MVP" | ARCO en M8 fue "plug & play" |

## Decisiones que hice mal y corregí

| Error | Corrección | ADR |
|-------|-----------|-----|
| Considerar Redis para el MVP | Eliminado — JWT no necesita estado | ADR-0012 |
| Planear "grupos conectados" | Eliminado — scope inmanejable sin diseño | ADR-0006 |
| file_ref expuesto en CopyResponse | Cortado antes de merge — ahora nunca se serializa | ADR-0009 |
| Encriptación a nivel de app como M8 | Diferida — key management merece v1 con review legal | ADR-0018 |

## Patrones recurrentes

1. **"Si no está en la spec, no se implementa"** — previno scope creep en CADA milestone.
2. **"Si viola un ADR, es un bug"** — detectó 4 regresiones antes de merge.
3. **"El AI pregunta, no asume"** — evitó malentendidos en casos edge.
4. **"Tests para cada property"** — la red de seguridad que permite refactorear sin miedo.

## Consejo para quien quiera replicar esto

1. **Empieza con 3-5 ADRs fundacionales.** No necesitas 18 el día 1, pero sí las decisiones que definen qué eres y qué no eres.
2. **Una spec por módulo, no más.** No crees documentación que no vas a mantener.
3. **Privacy y auth primero.** Son los cimientos más difíciles de agregar después.
4. **Steering rules > prompts repetidos.** Si algo es permanente, hazlo una regla, no una instrucción.
5. **Define "done" antes de empezar.** Si no sabes cuándo parar, nunca paras.
