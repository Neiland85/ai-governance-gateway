# ADR-002 — Branching & Merge Strategy

## Estado
Aceptado

## Contexto
El repositorio adopta un flujo Git orientado a gobernanza, trazabilidad y control
de cambios, evitando commits directos en ramas publicables y separando integración
de publicación.

## Decisión
Se adopta el siguiente modelo de ramas:

- `main`: rama gobernada, publicable, auditable.
- `dev`: aduana técnica e integración progresiva.
- `feature/*`: trabajo aislado y descartable.

## Reglas operativas

- Está prohibido hacer commits directos en `main`.
- Todo cambio entra vía Pull Request.
- `feature/* → dev` se integra mediante **squash merge**.
- `dev → main` se integra mediante **merge commit**.
- Cambios estructurales requieren ADR.

## Consecuencias

- Historial limpio y explicable en `main`.
- `dev` conserva contexto de integración.
- Fácil auditoría y rollback.

## Alternativas consideradas

- GitFlow completo: descartado por sobrecarga.
- Trunk-based: descartado por falta de aduana.

## Nota final

> `main` cuenta la historia.
> `dev` absorbe la complejidad.

