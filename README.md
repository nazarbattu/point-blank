# Point Blank

![CI](https://github.com/nazarbattu/point-blank/actions/workflows/ci.yml/badge.svg)

Fitness and nutrition tracker backend (Identity, Diet, Exercise). Built as a staged architecture journey: modular monolith first, then microservices, Kafka, Redis, and Spring AI — so each technology is introduced for a clear reason and can be measured.

**Stack:** Java 21, Spring Boot, PostgreSQL (later Kafka, Redis, Spring AI)

## Local Postgres

```bash
docker compose up -d
```

## Build

```bash
./mvnw clean verify
```
