# Tool Gateway — Diseño Conceptual (v1)

## Propósito

El **Tool Gateway** es el componente responsable de **ejecutar herramientas** (acciones técnicas) de forma controlada, **sin lógica de decisión**, **sin evaluación de políticas** y **sin interpretación del contexto**.

Es el **único punto autorizado de ejecución**. Actúa como **Policy Enforcement Point (PEP)** físico.

---

## Rol en el sistema

- Consume solicitudes de ejecución (`tool.exec.request`).
- Verifica la **autorización firmada** emitida por el Guardian.
- Aplica la **allowlist** declarativa (tools/scopes/mappings).
- Ejecuta la herramienta mediante un **plugin/adapter**.
- Emite resultado (`tool.exec.result`) y eventos de auditoría.

El Tool Gateway **no**:
- decide
- evalúa GOV-01/02/03
- corrige outputs
- amplía scopes

---

## Entradas (inputs)

Entrada principal: `tool_exec_request` validado contra `schemas/`.

Incluye:
- `authorization`
  - `authz_id`
  - `tool`
  - `scopes`
  - `constraints`
  - `issued_at` / `expires_at`
  - `nonce`
  - `signature`
- `tool_call`
  - nombre de tool
  - args

Todos los inputs deben validar contra `schemas/`.

---

## Salidas (outputs)

- `tool_exec_result`
  - `status: ok|error`
  - resultado estructurado
  - referencia de logs (si aplica)
- `audit_event`
  - intento permitido/bloqueado
  - razón
  - hashes
  - correlación

---

## Flujo de ejecución (alto nivel)

1. Validación estructural (schemas)
2. Validación criptográfica de autorización
   - firma válida
   - expiración
   - emisor
3. Anti-replay
   - `nonce` no reutilizado (KV)
4. Validación allowlist
   - tool existe y habilitada
   - scope existe
   - mapping tool↔scope permitido
5. Validación de constraints
   - paths, branches, hosts, methods, subjects, etc.
6. Ejecución del plugin
7. Emisión de resultado + auditoría

---

## Invariantes (no negociables)

- Sin autorización válida → **BLOCK**
- Autorización expirada → **BLOCK**
- Nonce reutilizado → **BLOCK**
- Tool no permitida → **BLOCK**
- Scope no permitido → **BLOCK**
- Constraints violadas → **BLOCK**

El Tool Gateway no debe tener "modo permissive" en producción.

---

## Modelo de plugins (conceptual)

El Tool Gateway ejecuta tools mediante adaptadores:

- `git.read`
- `git.commit`
- `nats.publish`
- `http.request` (deshabilitada por defecto)

Cada plugin:
- valida args según schema
- ejecuta acción
- devuelve resultado estructurado

---

## Relación con otros componentes

- **Guardian**
  - emite autorizaciones firmadas
- **Orchestrator**
  - solicita ejecución solo tras `APPROVE`
- **Core**
  - define allowlist, contracts, messaging

El Tool Gateway es sustituible, pero su interfaz no.

---

## Estado

Diseño conceptual v1.

No hay implementación.
No hay runtime.

La implementación vivirá en un repositorio separado.


