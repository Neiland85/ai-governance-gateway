# Orchestrator — Diseño Conceptual (v1)

## Propósito

El **Orchestrator** es el componente responsable de **coordinar flujos**, enrutar mensajes y **orquestar interacciones** entre agentes, el Guardian y el Tool Gateway.

No toma decisiones de gobernanza. **Ejecuta el flujo**.

---

## Rol en el sistema

* Actúa como **Workflow Coordinator**.
* Recibe eventos/solicitudes iniciales.
* Normaliza y valida inputs contra `schemas/`.
* Solicita decisiones al **Guardian**.
* Enruta autorizaciones al **Tool Gateway**.
* Emite resultados y eventos de auditoría.

El Orchestrator **no**:

* evalúa políticas
* emite autorizaciones
* ejecuta herramientas

---

## Entradas (inputs)

* `request`

  * intención
  * dominio
  * contexto
* `agent_output`

  * estructura validada
* `guardian_decision`

  * decisión + (opcional) autorización

Todas las entradas deben cumplir los **schemas del core**.

---

## Salidas (outputs)

* Eventos de progreso del workflow
* Solicitudes al Tool Gateway (solo con autorización)
* Eventos de auditoría
* Respuestas finales al solicitante

---

## Flujo de orquestación (alto nivel)

1. Recepción de solicitud
2. Validación estructural
3. Envío al Guardian para decisión
4. Branching según decisión:

   * APPROVE → continuar
   * RETURN_FOR_FIX → devolver corrección
   * BLOCK → finalizar
   * ESCALATE → notificar
5. (Si aplica) Envío al Tool Gateway
6. Emisión de eventos y cierre del flujo

---

## Invariantes

* Sin decisión del Guardian → **no hay ejecución**
* Sin autorización → **no hay Tool Gateway**
* Todo paso genera traza auditable

---

## Relación con otros componentes

* **Guardian**

  * autoridad decisional
* **Tool Gateway**

  * ejecución controlada
* **Core**

  * contratos y políticas

El Orchestrator es **reemplazable** y **configurable**.

---

## Estado

Diseño conceptual v1.

No hay implementación.
No hay runtime.

La implementación vivirá en un repositorio separado.

