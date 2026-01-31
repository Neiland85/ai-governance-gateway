# Guardian — Diseño Conceptual (v1)

## Propósito

El **Guardian** es el componente encargado de **evaluar, decidir y autorizar** acciones en sistemas basados en agentes, de acuerdo con el core de gobernanza definido en `ai-governance-gateway`.

No ejecuta lógica de negocio ni herramientas. **Decide**.

---

## Rol en el sistema

* Actúa como **Policy Decision Point (PDP)**.
* Consume outputs de agentes u orquestadores.
* Evalúa contra:

  * ADRs
  * Bundles de políticas (GOV-01/02/03)
  * Riesgo y contexto
* Emite decisiones **inmutables y trazables**.

El Guardian **no**:

* ejecuta tools
* muta estado externo
* corrige contenido

---

## Entradas (inputs)

El Guardian recibe **solo estructuras formales**, nunca texto libre:

* `agent_output`

  * claims (fact / inference / opinion)
  * evidencias
* `context`

  * intención
  * dominio
  * riesgo
* `policy_bundle`

  * versión explícita

Todos los inputs deben validar contra `schemas/`.

---

## Salidas (outputs)

El Guardian emite una **decisión firmada**:

* `APPROVE`
* `RETURN_FOR_FIX`
* `BLOCK`
* `ESCALATE`

Opcionalmente:

* `authorization` (solo si `APPROVE` y aplica GOV-03)

Cada output incluye:

* decisión
* justificación
* referencia a políticas
* hash del input evaluado

---

## Flujo de decisión (alto nivel)

1. Validación estructural (schemas)
2. Aplicación de GOV-01 (separación de capas)
3. Aplicación de GOV-02 (evidencia y trazabilidad)
4. Evaluación de riesgo
5. Aplicación de GOV-03 (si hay tooling)
6. Emisión de decisión

Este flujo es **determinista** para un mismo input + bundle.

---

## Invariantes

* Sin bundle explícito → **BLOCK**
* Sin evidencia mínima → **RETURN_FOR_FIX / BLOCK**
* Sin trazabilidad → **ESCALATE**
* Sin autorización explícita → **no hay ejecución**

---

## Relación con otros componentes

* **Core (`ai-governance-gateway`)**

  * fuente de verdad normativa
* **Orchestrator**

  * envía solicitudes
  * recibe decisiones
* **Tool Gateway**

  * consume autorizaciones
  * nunca consulta políticas

El Guardian es **reemplazable**, el core **no**.

---

## Estado

Diseño conceptual v1.

No hay implementación.
No hay runtime.
No hay dependencias.

La implementación vivirá en un repositorio separado.

