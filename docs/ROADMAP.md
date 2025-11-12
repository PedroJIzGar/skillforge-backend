# Roadmap — SkillForge

**Stack:** Spring Boot 3 • Spring Web/Security/Validation • Spring Data JPA • PostgreSQL • Flyway • Springdoc (Swagger)  
**Extras (sprints posteriores):** Redis Streams • SSE/WebSocket • Firebase Admin (verify ID token)

---

## Convenciones
- **Ramas:** `feature/<issue>-slug`, `fix/<issue>-slug`, `chore/<issue>-slug`, `spike/<issue>-slug`.
- **Commits:** conventional commits.
- **Issues:** plantillas + labels `type:*`, `Sprint: X`, `area: backend`.
- **PRs:** tests verdes, `Closes #<issue>`, descripción breve.

---

## Sprint 1 — Fundaciones + API Labs + Firebase verify (mín.)
**Meta:** API pública para catálogo y detalle; esquema DB versionado; verificación Firebase lista (aunque endpoints públicos al inicio).

### Entregables
- [ ] **Boot** proyecto + configuración `application.yml` (dev).
- [ ] **Flyway V1**: tablas `labs`, `lab_steps`.
- [ ] **Flyway V2**: seed demo (`ToDo API` + 2 steps).
- [ ] **GET `/api/labs`** (publicados + orden desc por `created_at`).
- [ ] **GET `/api/labs/{slug}`** (detalle + pasos ordenados).
- [ ] **Swagger** accesible.
- [ ] **Firebase Admin SDK** + filtro `OncePerRequestFilter` (solo verificación; autorización la activamos más tarde).
- [ ] **CORS** dev para `http://localhost:4200`.

### Criterios de aceptación
- [ ] Flyway aplica V1/V2 sin errores (local).
- [ ] `GET /api/labs` devuelve al menos 1 lab (seed).
- [ ] `GET /api/labs/{slug}` devuelve pasos ordenados.
- [ ] Swagger documenta ambos endpoints.
- [ ] Petición con token válido rellena `SecurityContext` (log/traza).
- [ ] CI Maven verde.

### Notas técnicas
- **DB:** `jdbc:postgresql://localhost:5432/skillforge`, user/pass `sf/sf`.
- **Flyway:** `classpath:db/migration` (`V1__init.sql`, `V2__seed.sql`).
- **Entidades:** `Lab`, `LabStep`; repos: `LabRepository`, `LabStepRepository`.
- **DTOs:** `LabDto`, `LabDetailDto`.
- **Security:** `SessionCreationPolicy.STATELESS`, CSRF off para API; por ahora `.permitAll()` salvo Swagger libre.

---

## Sprint 2 — Submissions + Mensajería + Tiempo real
**Meta:** Recibir envíos, publicar a cola y emitir progreso por SSE/WebSocket.

### Entregables
- [ ] `POST /api/submissions` (multipart ZIP + `labId`).
- [ ] Publisher a Redis Streams `submissions.validate`.
- [ ] Listener de `submission.updated` para persistir estado.
- [ ] Endpoint SSE `/api/submissions/{id}/stream`.

### Criterios de aceptación
- [ ] Subida de archivo persiste `submission` con estado inicial.
- [ ] Flujo de progreso en vivo hasta `PASSED/FAILED`.

---

## Sprint 3 — Admin + Métricas + Actuator
**Meta:** Endpoints admin para CRUD de labs/steps; métricas básicas; Actuator.

### Entregables
- [ ] `/api/admin/labs` y `/api/admin/steps` (CRUD).
- [ ] `/actuator/health`, `/metrics` (Micrometer).
- [ ] Stats: nº envíos, tasa éxito por lab, tiempo medio.

---

## Sprint 4 — Validator real + Observabilidad
**Meta:** Reglas reales de validación (p. ej., para `ToDo API`) y trazabilidad.

### Entregables
- [ ] Validación de endpoints (200/201/204 + esquema).
- [ ] Timeouts, reintentos, idempotencia por `submissionId`.
- [ ] (Opcional) RabbitMQ + DLQ.

---

## Checklist de configuración (dev)
- [ ] **Postgres**: base `skillforge`, user `sf`, pass `sf`.
- [ ] **Flyway** activo; `hibernate.ddl-auto=validate`.
- [ ] **Swagger** (springdoc) accesible en `/swagger-ui/index.html`.
- [ ] **CORS** habilitado para `http://localhost:4200`.
- [ ] **Firebase Admin**: var `GOOGLE_APPLICATION_CREDENTIALS` apuntando a la service account (si se prueba verificación).

---

## CI / Calidad
- [ ] `.github/workflows/ci.yml`: JDK 21, cache de Maven, `mvn -B -DskipTests=false verify`.
- [ ] Tests mínimos de controlador (200 y 404 en detalle).
- [ ] Regla de calidad básica: formatter + checkstyle opcional.

---

## Enlaces
- **Swagger:** `/swagger-ui/index.html`
- **Project board:** <añade URL si usas GitHub Projects>
