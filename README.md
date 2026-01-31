# AI Governance Gateway

## Intención del proyecto

Este repositorio implementa un núcleo de **gobernanza operativa** para sistemas basados en agentes y automatización con IA.

Su objetivo no es aumentar la capacidad de la IA, sino **controlar, autorizar y auditar** qué acciones pueden ejecutarse, en qué contexto y bajo qué políticas explícitas.

> La inteligencia propone.
> La gobernanza decide.
> La ejecución verifica.

## Qué ES este proyecto

- Un **Policy Decision Point (PDP)** para evaluar outputs de agentes
- Un **Policy Enforcement Point (PEP)** para autorizar la ejecución de herramientas
- Un sistema **event-driven** (NATS + JetStream)
- Un marco de **políticas versionadas**, trazables y auditables
- Un diseño orientado a **evitar bypasses**, humanos o técnicos

## Qué NO es este proyecto

- ❌ Un framework genérico de IA
- ❌ Un chatbot o asistente
- ❌ Un sistema de “alineación ética”
- ❌ Un producto SaaS listo para uso final
- ❌ Un sistema de decisión autónoma sin control humano

## Casos de uso previstos

- Sistemas multi-agente con herramientas reales
- Automatización con impacto técnico, legal u operativo
- CI/CD asistido por IA
- Legal-tech y sistemas auditables
- Entornos donde la trazabilidad es obligatoria

## Principios no negociables

- Deny by default
- Autorizaciones efímeras y firmadas
- Separación estricta entre decisión y ejecución
- Auditabilidad como requisito, no como añadido
- Ningún tool-call sin autorización explícita

## Alcance y límites

Este repositorio se centra exclusivamente en el **núcleo de gobernanza**:

- Definición de políticas
- Contratos de mensajería
- Allowlist de herramientas y scopes
- Modelos de riesgo y decisión

No incluye:
- Implementaciones de agentes
- Código de negocio
- Integraciones cloud
- Interfaces de usuario

El alcance está **deliberadamente limitado** para preservar claridad, seguridad y propósito.

## Stack tecnológico (definido y justificado)

Este repositorio **no es un producto ejecutable**, pero define explícitamente el **stack de referencia** sobre el que se apoya su diseño. Esto evita ambigüedades y desviaciones futuras.

### Mensajería y orquestación

- **NATS + JetStream**

**Por qué:**
- Latencia baja y semántica simple
- Soporte nativo para **event‑driven real**
- JetStream permite **durabilidad, reintentos, DLQ y KV** sin añadir sistemas externos
- Ideal para flujos con múltiples actores desacoplados

Este proyecto asume que **toda decisión y acción relevante es un evento**.

---

### Gobernanza y políticas

- **Políticas declarativas en YAML / Markdown**

**Por qué:**
- Versionables
- Auditables
- Revisables sin ejecutar código
- Aptas para revisión legal / compliance

Las políticas **no viven en código** para evitar acoplamiento con implementación.

---

### Servicios lógicos (no implementados aquí)

El diseño asume tres servicios independientes:

- **Orchestrator** (coordina flujos)
- **Governance Guardian** (decide y autoriza)
- **Tool Gateway** (ejecuta y verifica)

**Por qué:**
- Separación clara de responsabilidades
- Prevención de bypasses
- Trazabilidad completa de decisiones vs ejecución

La implementación concreta (NestJS, FastAPI, Go, etc.) queda **fuera del alcance** del repo.

---

### Criptografía

- **ed25519** para firma de autorizaciones

**Por qué:**
- Firmas rápidas
- Claves pequeñas
- Verificación simple
- Amplio soporte

Las autorizaciones son **efímeras, firmadas y verificables**.

---

### Persistencia auxiliar

- **JetStream KV** para:
  - estado de workflow
  - control de nonces (anti-replay)
  - cache de bundles de políticas

**Por qué:**
- Evita introducir bases de datos externas
- Consistencia con el bus de eventos
- Simplicidad operacional

---

## Versiones de referencia y requisitos iniciales

Este proyecto define **versiones mínimas recomendadas** para garantizar coherencia arquitectónica y evitar problemas de compatibilidad desde el inicio.

### Infraestructura base

- **NATS Server**: `>= 2.10.x`
  - JetStream y KV estables
  - Soporte completo de streams, consumers y backoff

- **JetStream**: integrado en NATS (`--js` habilitado)

---

### Lenguajes / runtimes (referencia, no obligatorios)

La implementación concreta queda fuera del alcance del repo, pero estas son las **referencias recomendadas**:

- **Go**: `>= 1.22`
  - Cliente NATS oficial, alto rendimiento
- **Node.js**: `>= 20 LTS`
  - Adecuado para Orchestrator / Tool Gateway
- **Python**: `>= 3.11`
  - Útil para Guardian o tooling auxiliar

---

### Ejemplo de archivos de requisitos (referencia)

Estos archivos **no forman parte obligatoria del repo**, pero se incluyen como guía de arranque.

#### `requirements.txt` (Python – Guardian)
```text
nats-py>=2.7.0
pydantic>=2.6
pyyaml>=6.0
cryptography>=42.0
```

#### `package.json` (Node.js – Orchestrator / Tool Gateway)
```json
{
  "engines": { "node": ">=20" },
  "dependencies": {
    "nats": "^2.17.0",
    "zod": "^3.23.0",
    "yaml": "^2.4.0"
  }
}
```

#### `go.mod` (Go – servicios de alto rendimiento)
```text
module ai-governance-gateway

go 1.22

require github.com/nats-io/nats.go v1.34.0
```

> Estos ejemplos sirven únicamente para **alinear expectativas técnicas**. El repo no impone lenguaje ni runtime.

Esto es **infraestructura de control**, no una plataforma de aplicación.

---

## `.gitignore` recomendado (inicio del proyecto)

Este repositorio es **principalmente declarativo**. El `.gitignore` inicial debe ser **conservador**, evitando ruido sin ocultar artefactos relevantes de gobernanza.

### `.gitignore` base

```gitignore
# OS
.DS_Store
Thumbs.db

# Editors / IDEs
.vscode/
.idea/
*.swp

# Logs / runtime
logs/
*.log

# Python
__pycache__/
*.py[cod]
.venv/

# Node.js
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Go
/bin/
*.exe
*.test
*.out

# Build artifacts (si se añaden en forks)
dist/
build/

# NATS local state (solo para entornos de desarrollo)
.nats/
jetstream/
```

**Principio:**
- Se ignora todo lo **derivado del entorno de desarrollo**.
- **No** se ignoran políticas, esquemas, contratos ni documentación.

---

## ADRs y cambios en `.gitignore`

Cualquier modificación del `.gitignore` **que afecte a la trazabilidad, auditabilidad o alcance del repo** debe estar respaldada por un **ADR (Architecture Decision Record)**.

## Documentation

- [ADRs](docs/adrs/)
- [Workflows](docs/workflows/)
- [Architecture](docs/architecture/)

### Cuándo es obligatorio crear un ADR

Crear un ADR si ocurre cualquiera de estos casos:

- Se decide **ignorar nuevos tipos de artefactos** (p. ej. resultados de ejecución, snapshots, hashes).
- Se añade código ejecutable y se amplía el `.gitignore` para ocultar nuevos outputs.
- Se cambia el tratamiento de **logs, evidencias o estados persistentes**.
- El repo deja de ser puramente declarativo.

### Cuándo NO es necesario un ADR

- Ajustes menores por tooling local (IDE, editor).
- Exclusiones claramente temporales en forks privados.

### Recomendación práctica

- Mantener este `.gitignore` **estable en el repo público**.
- Documentar cualquier cambio significativo mediante un ADR del tipo:

```
ADR-00X-gitignore-scope-change.md
```

Con esto, incluso el `.gitignore` queda **bajo gobernanza explícita**.

## Estado del proyecto

MVP de arquitectura y gobernanza. Diseñado para ser **reutilizado**, **auditado** y **extendido mediante forks privados** cuando sea necesario.


---

## Policy Bundle v1 — Núcleo de decisión

Este proyecto define un **primer bundle de políticas** que gobierna cómo se evalúan outputs y cuándo se permiten acciones.

### Ubicación

```
governance/bundles/2026.01.31+1/
```

### Contenido del bundle

- `manifest.yml` — Identidad, alcance y hash del bundle
- `GOV-01-separacion-capas.md` — Hechos vs inferencias vs opiniones
- `GOV-02-evidencia-minima.md` — Evidencia, trazabilidad y reevaluación
- `GOV-03-autorizaciones-tooling.md` — Condiciones para firmar autorizaciones
- `risk-matrix.yml` — Clasificación objetiva de riesgo
- `decision-thresholds.yml` — Umbrales por entorno (dev/prod)

### Principio operativo

> El Guardián **no decide por utilidad**, decide por **política explícita**.

---

## GOV-01 — Separación estricta de capas

### Objetivo

Garantizar que todo output evaluado por el sistema distingue de forma **explícita y verificable** entre:

- **Hechos (facts)**
- **Inferencias (inferences)**
- **Opiniones (opinions)**

Evitar que inferencias u opiniones se presenten como hechos es un requisito **crítico de gobernanza**.

---

### Definiciones formales

- **Hecho (fact)**  
  Afirmación verificable basada en una fuente identificable (documento, log, estándar, entrada de usuario).

- **Inferencia (inference)**  
  Conclusión lógica derivada de uno o más hechos, sujeta a interpretación.

- **Opinión (opinion)**  
  Juicio de valor, recomendación o preferencia no verificable de forma objetiva.

---

### Reglas obligatorias

1. **Toda afirmación debe estar clasificada** como `fact`, `inference` u `opinion`.
2. Todo `fact` **debe incluir una fuente** o declararse explícitamente como no verificado.
3. Ninguna `inference` puede presentarse como `fact`.
4. Las `opinions` deben estar claramente separadas del resto del contenido.

---

### Violaciones y decisiones

| Violación detectada | Decisión del Guardián |
|-------------------|----------------------|
| Falta de clasificación | RETURN_FOR_FIX |
| Hecho sin fuente | RETURN_FOR_FIX |
| Inferencia presentada como hecho | BLOCK |
| Opinión mezclada con hechos | RETURN_FOR_FIX |
| Violación reiterada | ESCALATE |

---

### Justificación

Esta política existe para:

- Reducir riesgo legal y reputacional
- Facilitar auditoría y defensa técnica
- Evitar ambigüedad intencionada o accidental
- Permitir decisiones automáticas **explicables**

Sin esta separación, ningún output es gobernable.

---

### Relación con otras políticas

- **GOV-02** depende de GOV-01 para evaluar evidencia.
- **GOV-03** asume outputs ya normalizados por GOV-01.

Cualquier modificación de esta política requiere **nuevo bundle de versión**.


---

## GOV-02 — Evidencia mínima y trazabilidad

### Objetivo

Garantizar que todo output con impacto **técnico, legal, operativo o reputacional** esté respaldado por un nivel mínimo de **evidencia verificable** y que cada decisión sea **trazable y reproducible**.

Esta política define **cuándo un output es defendible** y **cuándo no puede avanzar** en el flujo.

---

### Principios

1. **Sin evidencia no hay autorización**.
2. La evidencia debe ser **proporcional al riesgo**.
3. Toda decisión relevante debe dejar **traza técnica persistente**.
4. Las mutaciones requieren **reevaluación posterior obligatoria**.

---

### Tipos de evidencia aceptada

- **Documental**: estándares, contratos, políticas, ADRs, documentación oficial.
- **Técnica**: logs, métricas, hashes, configuraciones, estados de sistema.
- **Entrada de usuario**: declarada explícitamente como tal.
- **Inferida**: solo aceptable si está claramente etiquetada como `inference`.

> La ausencia de evidencia debe declararse explícitamente.

---

### Reglas obligatorias

1. Todo `fact` con impacto **medio o alto** debe incluir evidencia verificable.
2. Si la evidencia no está disponible, el output debe:
   - declararlo explícitamente, y
   - limitar su alcance.
3. Ninguna **acción de tipo mutación** puede autorizarse sin evidencia suficiente.
4. Toda ejecución de tool genera un evento de auditoría **append-only**.
5. Tras cualquier mutación, el output debe ser **reevaluado** por el Guardián.

---

### Violaciones y decisiones

| Situación detectada | Decisión del Guardián |
|--------------------|----------------------|
| Fact sin evidencia (riesgo bajo) | RETURN_FOR_FIX |
| Fact sin evidencia (riesgo medio) | BLOCK |
| Fact sin evidencia (riesgo alto) | ESCALATE |
| Mutación sin evidencia | BLOCK |
| Evidencia declarada pero inconsistente | RETURN_FOR_FIX |
| Repetición sistemática | ESCALATE |

---

### Trazabilidad mínima requerida

Cada decisión del Guardián debe poder reconstruirse a partir de:

- `correlation_id`
- bundle de políticas activo
- versión del Guardián
- output evaluado (o hash del mismo)
- decisión emitida
- justificación normativa

Esta información debe persistirse en el **Audit Stream**.

---

### Justificación

Esta política existe para:

- Hacer los outputs **defendibles ante terceros**
- Reducir riesgo legal y reputacional
- Permitir auditorías técnicas completas
- Evitar automatismos opacos

Un sistema sin evidencia trazable **no es gobernable**.

---

### Relación con otras políticas

- **GOV-01** define qué es un hecho, inferencia u opinión.
- **GOV-03** utiliza GOV-02 para decidir si puede firmarse una autorización.

Cualquier modificación de esta política requiere **nuevo bundle de versión**.

---

## GOV-03 — Autorizaciones de tooling

### Objetivo

Definir **cuándo y cómo** el sistema puede autorizar la ejecución de herramientas que producen **efectos materiales** (mutaciones técnicas, llamadas externas, cambios persistentes).

Esta política separa de forma estricta:
- **decisión** (Gobernanza),
- **autorización** (permiso firmado), y
- **ejecución** (Tool Gateway).

---

### Principios

1. **Deny by default**: ninguna herramienta se ejecuta sin autorización explícita.
2. Las autorizaciones son **efímeras, acotadas y firmadas**.
3. La autorización **no implica corrección** del output.
4. Toda mutación exige **reevaluación posterior**.

---

### Condiciones necesarias (todas obligatorias)

Para emitir una autorización, el Guardián **debe** verificar:

1. La decisión previa es `APPROVE`.
2. El output cumple **GOV-01** (separación de capas).
3. El output cumple **GOV-02** (evidencia suficiente).
4. El riesgo efectivo está **dentro de los umbrales** del entorno activo.
5. La herramienta está permitida por la **allowlist**.
6. El scope solicitado es **mínimo y explícito**.
7. Existe una **justificación técnica** clara de la acción.
8. Se define un **TTL** acorde al entorno.

Si alguna condición falla → **no se emite autorización**.

---

### Condiciones prohibidas (cualquiera bloquea)

- Uso ambiguo o no declarado.
- Solicitud de scope más amplio del necesario.
- Herramientas de tipo `mutation` en `prod` sin escalado humano.
- Reutilización de `nonce`.
- Autorizaciones sin expiración.

**Decisión:** `BLOCK` o `ESCALATE` según criticidad.

---

### Contenido mínimo de una autorización

Toda autorización emitida debe incluir:

- `authz_id`
- `tool`
- `scopes` autorizados
- `constraints` técnicas
- `issued_at` y `expires_at`
- `nonce`
- `signature` criptográfica

Las autorizaciones son **no reutilizables** y **no ampliables**.

---

### Reglas por entorno (referencia)

- **dev**
  - TTL máximo: 300s
  - Mutaciones permitidas sin escalado

- **prod**
  - TTL máximo: 60s
  - Mutaciones **requieren escalado humano**

---

### Violaciones y decisiones

| Situación detectada | Decisión del Guardián |
|--------------------|----------------------|
| Tool no permitido | BLOCK |
| Scope excesivo | BLOCK |
| Evidencia insuficiente | BLOCK |
| Riesgo medio en prod | ESCALATE |
| Autorización válida | APPROVE |

---

### Justificación

Esta política existe para:

- Evitar bypasses técnicos y humanos
- Limitar el impacto de errores automatizados
- Separar claramente responsabilidad
- Hacer cada acción **atribuible y defendible**

La ejecución sin autorización explícita es considerada **violación crítica de gobernanza**.

---

### Relación con otras políticas

- **GOV-01** normaliza el contenido evaluado.
- **GOV-02** valida si existe evidencia suficiente.
- Esta política es la **única** que permite efectos materiales.

Cualquier modificación de esta política requiere **nuevo bundle de versión**.

---

## Tool Gateway — Allowlist (control efectivo de ejecución)

Esta sección materializa **GOV-03** en control técnico real. El Tool Gateway es el **único componente autorizado** a ejecutar herramientas y lo hace exclusivamente contra una **allowlist declarativa**.

### Principios

- **Deny by default**
- Tool ≠ Scope
- Permisos mínimos
- Nada implícito, nada heredado

---

### `tool-gateway/allowlist/tools.yml`

```yaml
version: 1
tools:
  git.read:
    description: Lectura de repositorio Git
    class: read
    risk: low
    args_schema:
      path: string

  git.commit:
    description: Commit en repositorio Git
    class: mutation
    risk: medium
    requires_reapproval: true
    args_schema:
      message: string
      files: optional[array[string]]

  http.request:
    description: Llamadas HTTP salientes
    class: external
    risk: high
    disabled_by_default: true
```

---

### `tool-gateway/allowlist/scopes.yml`

```yaml
version: 1
scopes:
  repo:a4co:
    type: git-repo
    description: Repositorio A4CO
    constraints:
      branches: ["main", "develop"]
      paths_allow:
        - "docs/**"
        - "src/**"
      paths_deny:
        - ".github/workflows/**"

  http:github_api:
    type: http
    description: GitHub REST API
    constraints:
      host_allow:
        - "api.github.com"
      methods_allow: ["GET"]
```

---

### `tool-gateway/allowlist/mappings.yml`

```yaml
version: 1
mappings:
  git.read:
    allowed_scopes:
      - repo:a4co

  git.commit:
    allowed_scopes:
      - repo:a4co

  http.request:
    allowed_scopes:
      - http:github_api
```

---

### Reglas duras de ejecución

- Si la tool **no existe** → BLOCK
- Si el scope **no existe** → BLOCK
- Si no hay mapping tool↔scope → BLOCK
- Si la tool está `disabled_by_default` → BLOCK
- Si las `constraints` no se cumplen → BLOCK

Toda ejecución (permitida o bloqueada) genera un **evento de auditoría append-only**.

---

### Relación con las políticas

- **GOV-03** define cuándo se puede emitir una autorización.
- La allowlist define **qué es técnicamente ejecutable**.
- El Tool Gateway **no interpreta intención**, solo verifica permiso.

La allowlist es el **límite físico del sistema**.


---

## Tool Gateway Allowlist (MVP)

El Tool Gateway aplica control **deny-by-default** mediante una allowlist declarativa. Esta allowlist define:

- **Tools**: capacidades técnicas disponibles
- **Scopes**: superficies concretas donde pueden operar
- **Mappings**: relación permitida tool ↔ scope

Ubicación:

```text
tool-gateway/allowlist/
  tools.yml
  scopes.yml
  mappings.yml
```

### `tools.yml` (catálogo de tools)

```yaml
version: 1
tools:
  git.read:
    description: Lectura de repositorio Git
    class: read
    risk: low
    args_schema:
      path: string

  git.commit:
    description: Commit en repositorio Git
    class: mutation
    risk: medium
    requires_reapproval: true
    args_schema:
      message: string
      files: optional[array[string]]

  nats.publish:
    description: Publicación controlada de eventos
    class: internal
    risk: low
    args_schema:
      subject: string
      payload: object

  http.request:
    description: Llamada HTTP saliente (restricciones fuertes)
    class: external
    risk: high
    disabled_by_default: true
    args_schema:
      method: string
      url: string
      headers: optional[object]
      body: optional[object]

  db.read:
    description: Lectura de base de datos (si aplica en forks)
    class: read
    risk: medium
    disabled_by_default: true
    args_schema:
      query: string
```

### `scopes.yml` (superficies y constraints)

```yaml
version: 1
scopes:
  repo:ai-governance-gateway:
    type: git-repo
    description: Repositorio de gobernanza
    constraints:
      branches: ["main", "develop"]
      paths_allow:
        - "README.md"
        - "governance/**"
        - "tool-gateway/**"
        - "messaging/**"
        - "schemas/**"
        - "docs/**"
      paths_deny:
        - ".github/workflows/**"

  nats:prod_core:
    type: nats
    description: Publicación en subjects permitidos (solo internos)
    constraints:
      subject_allow:
        - "prod.audit.event.v1"
        - "prod.workflow.*"
        - "prod.req.*"
        - "prod.task.*"
        - "prod.agent.*"
        - "prod.guardian.*"
        - "prod.tool.*"

  http:github_api_ro:
    type: http
    description: GitHub API (solo GET)
    constraints:
      host_allow: ["api.github.com"]
      methods_allow: ["GET"]
```

### `mappings.yml` (tool ↔ scope)

```yaml
version: 1
mappings:
  git.read:
    allowed_scopes:
      - repo:ai-governance-gateway

  git.commit:
    allowed_scopes:
      - repo:ai-governance-gateway

  nats.publish:
    allowed_scopes:
      - nats:prod_core

  http.request:
    allowed_scopes:
      - http:github_api_ro

  db.read:
    allowed_scopes: []
```

### Reglas de enforcement (Tool Gateway)

El Tool Gateway debe bloquear si:

- Tool no existe o está deshabilitada por entorno
- Scope no existe
- No existe mapping tool↔scope
- `authorization.signature` inválida o expirada
- `nonce` ya usado (anti-replay)
- Args no cumplen `args_schema`
- Constraints del scope no se cumplen (branch/path/host/method/subject)

> Nota: esta allowlist es el **mínimo seguro**. Cualquier ampliación requiere ADR si cambia el alcance o afecta a auditabilidad.


---

## Messaging / JetStream (MVP declarativo)

Este bloque define la **infraestructura de mensajería reproducible** que soporta el sistema de gobernanza. Todo es **event-driven**, durable y auditable.

### Ubicación

```text
messaging/
  subjects.md
  streams.yml
  consumers.yml
  kv.yml
```

---

### `subjects.md`

Convención: `<env>.<dominio>.<entidad>.<acción>.v<maj>`

**Core (prod)**

- `prod.req.workflow.start.v1`
- `prod.task.agent.run.v1`
- `prod.task.agent.fix.v1`
- `prod.agent.output.produced.v1`
- `prod.guardian.decision.issued.v1`
- `prod.tool.exec.request.v1`
- `prod.tool.exec.result.v1`
- `prod.workflow.final.output.v1`
- `prod.workflow.escalation.created.v1`
- `prod.audit.event.v1`

**DLQ**
- `prod.dlq.workflow.*.v1`
- `prod.dlq.tools.*.v1`

Headers obligatorios:
- `x-correlation-id`
- `x-tenant-id`
- `x-env`
- `x-policy-bundle-id`
- `x-event-id`
- `x-causation-id`

---

### `streams.yml`

```yaml
streams:
  WORKFLOW:
    subjects:
      - prod.req.workflow.start.v1
      - prod.task.agent.run.v1
      - prod.task.agent.fix.v1
      - prod.agent.output.produced.v1
      - prod.guardian.decision.issued.v1
      - prod.workflow.final.output.v1
      - prod.workflow.escalation.created.v1
    storage: file
    retention: limits
    max_age: 168h
    max_bytes: 20GB
    max_msg_size: 1MB
    replicas: 3

  TOOLS:
    subjects:
      - prod.tool.exec.request.v1
      - prod.tool.exec.result.v1
    storage: file
    retention: limits
    max_age: 72h
    max_bytes: 10GB
    max_msg_size: 512KB
    replicas: 3

  AUDIT:
    subjects:
      - prod.audit.event.v1
    storage: file
    retention: limits
    max_age: 8760h
    max_bytes: 200GB
    max_msg_size: 512KB
    replicas: 3

  DLQ:
    subjects:
      - prod.dlq.workflow.*.v1
      - prod.dlq.tools.*.v1
    storage: file
    retention: limits
    max_age: 168h
    max_bytes: 10GB
    replicas: 3
```

---

### `consumers.yml`

```yaml
consumers:
  agent_workers:
    stream: WORKFLOW
    filter_subject: prod.task.agent.*.v1
    ack_policy: explicit
    ack_wait: 60s
    max_deliver: 5
    backoff: [1s, 5s, 30s, 120s]

  guardian_eval:
    stream: WORKFLOW
    filter_subject: prod.agent.output.produced.v1
    ack_policy: explicit
    ack_wait: 30s
    max_deliver: 3
    backoff: [1s, 5s, 20s]

  tool_gateway:
    stream: TOOLS
    filter_subject: prod.tool.exec.request.v1
    ack_policy: explicit
    ack_wait: 30s
    max_deliver: 3
    backoff: [1s, 10s, 60s]
```

---

### `kv.yml`

```yaml
kv:
  workflow_state:
    history: 10
    ttl: 168h
    max_bytes: 2GB
    replicas: 3

  nonce_store:
    history: 1
    ttl: 300s
    max_bytes: 256MB
    replicas: 3

  policy_bundle_cache:
    history: 3
    ttl: 3600s
    max_bytes: 512MB
    replicas: 3
```

---

### Invariantes operativos

- Ack explícito en todos los consumers
- Reintentos finitos con backoff
- DLQ manual (no reprocesado automático)
- `AUDIT` append-only (sin compactación)
- Toda mutación exige reevaluación

Este bloque hace que la gobernanza sea **operacionalmente real**, no teórica.


---

## Schemas (contratos JSON del sistema)

Los schemas definen los **contratos verificables** entre componentes. Todo mensaje intercambiado debe validar contra estos esquemas.

Ubicación:

```text
schemas/
  envelope.json
  agent_output.json
  guardian_decision.json
  tool_exec_request.json
```

---

### `envelope.json`

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "required": ["event_id", "timestamp", "correlation_id", "actor", "type", "payload"],
  "properties": {
    "event_id": { "type": "string", "format": "uuid" },
    "timestamp": { "type": "string", "format": "date-time" },
    "correlation_id": { "type": "string", "format": "uuid" },
    "causation_id": { "type": "string", "format": "uuid" },
    "tenant_id": { "type": "string" },
    "env": { "type": "string", "enum": ["dev", "prod"] },
    "actor": { "type": "string" },
    "type": { "type": "string" },
    "policy_bundle": {
      "type": "object",
      "properties": {
        "id": { "type": "string" },
        "hash": { "type": "string" }
      }
    },
    "risk_profile": {
      "type": "object",
      "properties": {
        "legal": { "type": "string" },
        "reputational": { "type": "string" },
        "operational": { "type": "string" }
      }
    },
    "payload": { "type": "object" }
  }
}
```

---

### `agent_output.json`

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "required": ["content", "format", "claims"],
  "properties": {
    "content": { "type": "string" },
    "format": { "type": "string", "enum": ["markdown", "json", "text"] },
    "claims": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["type", "text"],
        "properties": {
          "type": { "type": "string", "enum": ["fact", "inference", "opinion"] },
          "text": { "type": "string" },
          "source": { "type": "string" }
        }
      }
    },
    "actions_suggested": {
      "type": "array",
      "items": { "type": "object" }
    }
  }
}
```

---

### `guardian_decision.json`

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "required": ["decision"],
  "properties": {
    "decision": { "type": "string", "enum": ["APPROVE", "BLOCK", "RETURN_FOR_FIX", "ESCALATE"] },
    "severity": { "type": "string" },
    "violations": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "policy": { "type": "string" },
          "description": { "type": "string" },
          "evidence_pointer": { "type": "string" }
        }
      }
    },
    "authorization": { "type": "object" }
  }
}
```

---

### `tool_exec_request.json`

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "required": ["authorization", "tool_call"],
  "properties": {
    "authorization": { "type": "object" },
    "tool_call": {
      "type": "object",
      "required": ["tool"],
      "properties": {
        "tool": { "type": "string" },
        "args": { "type": "object" }
      }
    }
  }
}
```

---

### Regla global

> Ningún mensaje que no valide contra su schema correspondiente puede avanzar en el flujo.

Con estos schemas, el sistema queda **formalmente cerrado, validable y publicable**.
