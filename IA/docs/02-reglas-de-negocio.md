# Reglas de Negocio

→ Convenciones: [00-README.md](00-README.md)  
→ Decisiones relacionadas: [04-decisiones.md](04-decisiones.md)

Este documento consolida las reglas de negocio **sin contradicciones ni menciones a ingredientes**. Cuando una regla del documento fuente fue corregida o eliminada, queda registrado en el [Changelog](#changelog) al final.

---

## 1. Auditoría de consistencia — Inconsistencias encontradas

Las siguientes inconsistencias fueron detectadas en el documento fuente `Project_Context_Conversational_Commerce.md` v2.0.0.

### 1.1 Secciones ausentes del archivo fuente principal

El documento numera secciones 1–10, luego salta directamente a §22.2, §24, §25, §26, §29 y §30.  
Las secciones §11–§21, §23, §27–§28 existen pero se encuentran en el archivo temporal (ignorado por instrucción del dueño del proyecto hasta que se solicite explícitamente su incorporación).

> **Estado:** Pendiente de incorporar cuando el dueño del proyecto lo indique. Las reglas derivables del archivo principal se documentan aquí como "Confirmado". Lo que dependa de las secciones ausentes queda registrado en [05-pendientes-y-riesgos.md](05-pendientes-y-riesgos.md).

### 1.2 Mención de ingredientes en ejemplo de disponibilidad

**§6 original:** "Ejemplo: si un producto está temporalmente inactivo por falta de ingredientes, el agente no debe ofrecerlo como disponible."

El proyecto NO modela ingredientes (§2.1: "los conceptos del dominio deben evitar asumir… ingredientes"). El ejemplo usa un concepto que contradice la restricción del dominio.

> **Decisión propuesta:** Reemplazar por: "si un producto o servicio está marcado como inactivo o no disponible por el negocio, el agente no debe ofrecerlo." → Registrado en [Changelog](#changelog).

### 1.3 Agente de voz presentado como componente central sin decisión de alcance

§2 describe el agente de voz como "componente diferencial" y §3.1 lo presenta como flujo principal, pero el documento no explicita si es MVP o futuro.

> **Decisión propuesta:** Clasificar el agente de voz como Fase 2. → Ver [04-decisiones.md §DEC-001](04-decisiones.md).

### 1.4 Identidad del cliente: no definida

§7.1 dice "Está asociado a un cliente cuando la identidad del cliente está disponible", pero el documento no define cómo se establece esa identidad ni qué ocurre cuando no está disponible (pedidos anónimos, soporte, reordenar).

> **Decisión propuesta:** Implementar esquema híbrido: QR sin login, WhatsApp con teléfono como identidad natural, web con OTP opcional. → Ver [04-decisiones.md §DEC-003](04-decisiones.md).

### 1.5 Método de cumplimiento por canal: ambigüedad en QR

§3.3 dice que el cliente puede elegir el destino del pedido al usar QR (consumo local, para llevar, envío). §3.4 aclara que el método no debe asumirse por el canal de origen.  
Sin embargo, en la práctica, un QR colocado en una mesa implica "consumo local" como opción predeterminada. No hay regla explícita sobre si el negocio puede preconfigurar el método de cumplimiento para un QR específico.

> **Decisión propuesta:** El negocio puede asignar un método de cumplimiento predeterminado a cada código QR, pero el cliente puede cambiarlo si el negocio habilita esa opción. El backend valida la combinación QR + método.

### 1.6 Pagos: ausentes del flujo de pedido

El flujo de pedido (§3.1, §3.2, §3.3) no menciona pago en ningún paso. §25 los clasifica como aspecto futuro. No está claro en qué estado queda un pedido "confirmado" si no hay pago.

> **Decisión propuesta:** Para el MVP, "confirmado" significa que el negocio aceptó el pedido; el pago ocurre fuera de la plataforma (en persona, transferencia manual). La arquitectura debe preparar el punto de integración pero no implementarlo. → Ver [04-decisiones.md §DEC-004](04-decisiones.md).

### 1.7 §22.2 sin sección padre

El documento incluye "§22.2 Casos que debe soportar" sin que exista §22 ni §22.1. Es un fragmento huérfano.

> **Decisión propuesta:** Tratar el contenido de §22.2 como parte de los requisitos del agente conversacional (§10). Registrado en el Changelog.

---

## 2. Reglas consolidadas

### 2.1 Principio central del agente

El agente conversacional es una capa de orquestación, **no la autoridad de negocio**.  
El backend valida siempre: disponibilidad, precios, promociones, restricciones y cualquier otra regla. (**Confirmado** — §10.2)

### 2.2 Catálogo y disponibilidad

- Un negocio puede marcar cualquier producto o servicio como inactivo o no disponible. (**Confirmado**)
- El agente debe consultar el estado actual antes de ofrecer o aceptar una selección. (**Confirmado**)
- Si un producto o servicio está marcado como inactivo, el agente no debe ofrecerlo como disponible. (**Confirmado** — §6, con corrección de ingredientes)
- La decisión de disponibilidad la aplica el backend; el frontend y el agente solo consumen el estado autorizado. (**Confirmado**)

### 2.3 Pedidos

- Un pedido puede originarse desde cualquier canal soportado. (**Confirmado** — §7.1)
- Un pedido pertenece a un Tenant y a un Business. (**Confirmado**)
- Un pedido puede estar asociado a un cliente cuando su identidad está disponible; puede ser anónimo. (**Confirmado**)
- Un pedido conserva el precio aplicado en el momento de la operación; no depende del catálogo vivo para reconstruir el importe histórico. (**Confirmado** — §7.1)
- Un pedido debe validarse antes de confirmarse. (**Confirmado**)
- El método de cumplimiento lo determina la configuración del negocio, no el canal de origen. (**Confirmado** — §7.2)
- Un mismo canal puede originar distintos métodos de cumplimiento. (**Confirmado**)

#### Métodos de cumplimiento soportados

- Consumo local.
- Para llevar.
- Envío.
- Otros métodos que el negocio configure posteriormente. (**Confirmado** — §7.2)

### 2.4 Promociones y cupones

- Los negocios pueden configurar promociones con vigencia y cupones con código. (**Confirmado** — §8)
- El agente **nunca** decide si un cupón es válido; solicita la validación al backend y comunica el resultado. (**Confirmado**)
- Los cupones tienen límites de uso, reglas de elegibilidad, restricciones por cliente y por producto/categoría/pedido según el dominio. (**Confirmado**)

### 2.5 Reglas específicas por cliente

- Un negocio puede configurar reglas que determinan qué puede o no puede comprar un cliente o grupo de clientes. (**Confirmado** — §9)
- El motor de reglas por cliente reside en el backend; el agente puede preguntar, explicar y guiar, pero no substituye al motor. (**Confirmado**)

### 2.6 Canales

- Los canales son interfaces distintas sobre un mismo dominio. No generan modelos de negocio independientes. (**Confirmado** — §4)
- Canales soportados: voz (llamada), WhatsApp, QR, web. (**Confirmado**)

### 2.7 Códigos QR

- El negocio genera y administra sus códigos QR. (**Confirmado** — §5)
- Un QR puede colocarse en cualquier ubicación física o digital. (**Confirmado** — §3.3)
- El sistema no asume un método de cumplimiento fijo por el solo hecho de que el pedido venga de un QR. (**Confirmado** — §3.4)
- El negocio puede asignar un método de cumplimiento predeterminado a un QR específico. (**Decisión propuesta** — resuelve ambigüedad §1.5)

### 2.8 Multi-tenancy

- La plataforma es multi-tenant: múltiples negocios comparten la infraestructura con aislamiento de datos. (**Confirmado** — §2)
- El modelo de aislamiento para el MVP es base de datos compartida con identificador de tenant en todas las entidades. (**Decisión propuesta** — ver [04-decisiones.md §DEC-002](04-decisiones.md))

### 2.9 Fuera de alcance del dominio

- Ingredientes, recetas, insumos y composición de productos: **no modelados**. (**Confirmado** — §2.1, §6 corregido)
- Mesas y cocina como conceptos de dominio: **no modelados** salvo que una capacidad específica lo requiera. (**Confirmado**)
- Facturación empresarial: **fuera del alcance** indefinidamente en la versión inicial. (**Confirmado** — §24)
- Pagos: **fuera del MVP**, preparar punto de integración. (**Decisión propuesta**)

---

## Changelog

| # | Fecha | Tipo | Descripción |
|---|---|---|---|
| 001 | 2026-10-07 | Corrección | §6: eliminada mención "falta de ingredientes". Reemplazada por "marcado como inactivo o no disponible por el negocio". |
| 002 | 2026-10-07 | Corrección | §22.2 (huérfano): contenido absorbido como requisitos del agente conversacional (§10). |
| 003 | 2026-10-07 | Pendiente | §11–§21, §23, §27–§28 existen en archivo temporal; pendientes de incorporar al documento fuente principal cuando el dueño lo indique. |
| 004 | 2026-10-07 | Decisión propuesta | Método de cumplimiento predeterminado por QR: negocio puede configurarlo por código. |
| 005 | 2026-10-07 | Decisión propuesta | Estado de pedido "confirmado" en MVP no implica pago; el pago ocurre fuera de la plataforma. |
