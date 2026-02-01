# ADR-003: Gobernanza del repositorio y control de cambios

## Estado
**Aceptado** — 2026-02-01

## Contexto
El repositorio `ai-governance-gateway` es público con fines de
transparencia arquitectónica y demostración técnica, pero **no es open-source**.
Contiene artefactos de gobernanza (políticas, contratos, workflows de validación)
que requieren **control estricto de cambios**, trazabilidad y protección
de la autoría.

Era necesario definir un modelo explícito de gobernanza del repositorio para:
- evitar modificaciones accidentales en `main`
- asegurar que todo cambio pase por validación automatizada
- proteger los derechos de autor
- mantener una historia Git limpia y auditable

## Decisión

Se adopta un modelo de gobernanza basado en **Rulesets de GitHub** con las
siguientes decisiones clave:

### Rama principal (`main`)
- `main` es considerada **rama sagrada**.
- No se permiten pushes directos.
- Todo cambio debe entrar vía **Pull Request**.
- Se requiere **al menos una aprobación** antes de merge.
- Se exige que los **checks de CI** pasen correctamente.
- Se requiere **historia lineal** (squash o rebase).
- Se bloquean **force pushes** y **borrado de rama**.
- Se requieren **commits firmados** para proteger la autoría.

### Excepciones (bypass)
- Solo el propietario del repositorio (`Neiland85`) puede hacer bypass
  del ruleset en situaciones de emergencia.

### Integración continua
- El repositorio incluye un workflow de CI orientado a:
  - validación de documentación
  - linting de YAML con reglas de estilo de industria
  - validación básica de JSON
- El CI es **reproducible**, con versiones de herramientas fijadas.
- No se introducen dependencias falsas ni archivos dummy para satisfacer el CI.

### Alcance legal
- El repositorio es público, pero su contenido está protegido por
  derechos de autor.
- El uso, modificación o redistribución requiere autorización expresa
  según lo indicado en el archivo `LICENSE`.

## Consecuencias

### Positivas
- Mayor seguridad y control sobre `main`.
- Cambios siempre revisados y validados.
- Historia Git limpia y fácil de auditar.
- Protección explícita de la autoría y del trabajo intelectual.
- Menor deuda técnica en CI y workflows.

### Negativas
- Mayor fricción para cambios rápidos.
- Necesidad de PR incluso para cambios pequeños.

Estas consecuencias se consideran aceptables dado el carácter
arquitectónico y de gobernanza del repositorio.

## Alternativas consideradas

- Permitir pushes directos a `main`: descartado por riesgo.
- No exigir commits firmados: descartado por pérdida de trazabilidad.
- Usar reglas antiguas de protección de ramas: descartado en favor de
  Rulesets, que ofrecen mayor expresividad y control.

## Notas
Este ADR aplica exclusivamente a la gobernanza del repositorio
`ai-governance-gateway`. Otros repositorios del ecosistema pueden adoptar
reglas similares o adaptadas según su rol.
