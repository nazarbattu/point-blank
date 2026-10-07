# ADR-001: Phase 0 project setup and staged evolution

## Status

Accepted

## Date

2026-10-07

## Context

We are building a fitness and nutrition tracker backend (Identity, Diet, Exercise).
The long-term stack includes a modular monolith, microservices, Kafka, Redis, and Spring AI.

Implementing that stack all at once would make debugging hard and would not produce
comparable performance numbers for a resume. We therefore need:

1. A deliberate staged roadmap (monolith → services → messaging → cache → AI).
2. A minimal Phase 0 foundation: an empty Spring Boot app that builds in CI, local
   Postgres via Docker, coverage tooling wired (JaCoCo), and this ADR recorded.

Phase 0 deliberately excludes domain features, JWT, Flyway domain migrations,
Kafka, Redis, Spring AI, and benchmarks.

## Decision

### Staged evolution

Evolve the system in measured stages:

1. Project setup and CI (Phase 0) — this ADR
2. Modular monolith with clear module boundaries
3. Extract microservices behind an API gateway
4. Introduce Kafka for async communication
5. Add Redis caching
6. Add one Spring AI + pgvector feature

Each stage ends with: tests green, CI green, a benchmark (from Phase 1 onward),
a Git tag, and an ADR.

### Phase 0 foundation choices

| Choice | Decision |
|--------|----------|
| Language / runtime | Java 21 |
| Framework | Spring Boot (Maven project at repo root; Web + Actuator) |
| Build tool | Maven with committed wrapper (`mvnw`, `mvnw.cmd`, `.mvn/`) |
| Local database | PostgreSQL 16 via `docker-compose.yml` (healthchecked; app not connected yet) |
| CI | GitHub Actions (`.github/workflows/ci.yml`) on `push`/`pull_request` to `main` |
| CI build | Temurin JDK 21, `./mvnw --batch-mode clean verify` |
| Coverage | JaCoCo Maven plugin on `verify` (report only; no coverage gate yet) |
| Docs layout | `docs/adr/` for decision records (`docs/benchmarks/` and `docs/diagrams/` deferred to Phase 1) |
| Docs / discoverability | `README.md` with project summary, stack, run/build commands, and CI badge |
| Hosting | Public GitHub repo `nazarbattu/point-blank` |

### Phase 0 deliverables completed

All Phase 0 exit criteria are met:

- [x] Empty Spring Boot application (`TrackerApplication`) with a trivial context-load test
- [x] Maven Wrapper committed; `mvnw` marked executable for Linux CI
- [x] `docker-compose.yml` for Postgres (`fitness` DB/user/password, port 5432, named volume, healthcheck); confirmed healthy locally
- [x] GitHub Actions CI green on `main` (`./mvnw --batch-mode clean verify`)
- [x] JaCoCo plugin in `pom.xml`; HTML report at `target/site/jacoco/index.html` after `verify`
- [x] This ADR under `docs/adr/001-initial-setup.md`
- [x] `README.md` with project summary, stack line, Postgres/build instructions, and CI badge

## Consequences

### Positive

- Each later technology is introduced for a clear reason and can be measured on the same machine.
- Failures are easier to isolate (one change set per stage).
- CI gives a baseline: every push to `main` (and PRs) must compile and pass tests.
- Local Postgres is ready before Phase 1 persistence work.
- JaCoCo is wired early so coverage gates can be added in Phase 1 without redoing plugin setup.
- Resume claims can later cite real local benchmarks instead of guesses.

### Negative / tradeoffs

- The journey takes longer than shipping a single “final” architecture.
- Early stages will be replaced or split; some work is intentionally temporary.
- Local Docker Compose only — not a production Kubernetes setup.
- App does not yet use Postgres; compose is infrastructure prep only.
- JaCoCo has no enforcement threshold yet; low coverage is still allowed.
- Spring Boot milestone parent may require care with dependency resolution compared to a stable release line.


