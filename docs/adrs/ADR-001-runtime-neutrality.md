# ADR-001 — Runtime Neutrality vs Developer Environment Leakage

## Estado

**Aceptado** — 2026-01

## Contexto

El repositorio **AI Governance Gateway** define un núcleo de gobernanza **declarativo y agnóstico de runtime**. Durante la configuración del entorno de desarrollo se observa que, en ciertos sistemas (macOS + VS Code + pyenv), el comando `python` puede resolverse a un runtime gestionado por el entorno del desarrollador (p. ej. `pyenv`) aun cuando el repositorio **no define ni requiere** Python.

Este comportamiento es consecuencia de **herencia del entorno del sistema** y/o inyección por herramientas externas (IDE, launchd), y **no implica** que el proyecto utilice Python ni que exista una dependencia implícita.

## Decisión

Se adopta la **neutralidad de runtime a nivel de proyecto** y se acepta la **posible fuga del entorno del desarrollador** sin considerarla una violación de gobernanza.

En concreto:

- El repositorio **NO** declara runtime por defecto.
- La presencia de un runtime en el entorno local **NO** implica uso por parte del proyecto.
- Cualquier uso de runtime debe ser **explícito, local y documentado** (opt-in).

## Alcance

Esta decisión aplica a:

- El repositorio público **AI Governance Gateway**.
- Workspaces de lectura, revisión y definición de políticas.

No aplica a:

- Forks privados de implementación.
- Servicios ejecutables (Guardian, Orchestrator, Tool Gateway).

## Reglas operativas

1. **No se incluye** `.python-version`, `.nvmrc`, `go.mod` ni entornos virtuales en este repositorio.
2. **No se activan** runtimes automáticamente al abrir el workspace.
3. La configuración del workspace (`.vscode/settings.json`) refuerza la neutralidad.
4. La ejecución de herramientas o runtimes es **responsabilidad del fork de implementación**.

## Consecuencias

**Positivas**:
- El proyecto no queda bloqueado por variaciones del entorno local.
- Se mantiene la coherencia con los principios de gobernanza (deny-by-default, explicit opt-in).
- Se separa claramente **arquitectura del proyecto** de **ergonomía del desarrollador**.

**Negativas**:
- El prompt o la resolución de binarios puede mostrar runtimes no usados por el proyecto.
- Puede requerirse documentación adicional en forks de implementación.

Estas consecuencias son aceptables y gestionables.

## Alternativas consideradas

- **Forzar neutralidad absoluta del entorno**: descartado por impacto desproporcionado y bloqueo del progreso.
- **Eliminar herramientas del sistema**: descartado por ser invasivo y no alineado con el alcance del proyecto.

## Relación con otras decisiones

- Alineado con **GOV-01/02/03** (explicitación, trazabilidad, control).
- Precondición para definir **workflows explícitos** en forks de implementación.

## Nota final

> Un runtime presente en el entorno **no equivale** a un runtime utilizado por el proyecto.
> La gobernanza se define por contratos, políticas y decisiones explícitas.

