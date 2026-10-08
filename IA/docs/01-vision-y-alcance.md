# Visión y Alcance

→ Convenciones de etiquetas: [00-README.md](00-README.md)

---

## 1. Problema que resuelve

Los negocios pequeños y medianos dependen de personas para tomar pedidos, lo que genera cuellos de botella en horas pico, errores de transcripción, pérdida de ventas fuera de horario y falta de visibilidad centralizada de la operación.

La plataforma reduce esa dependencia al ofrecer:

- Atención conversacional automatizada en múltiples canales (WhatsApp, QR, voz, web).
- Catálogo configurable con disponibilidad en tiempo real.
- Pedidos centralizados independientemente del canal de origen.
- Dashboard operativo para el negocio.
- Motor de reglas de negocio en el backend (precios, promociones, restricciones por cliente).

(**Confirmado** — documento fuente §2.2)

---

## 2. Tipos de usuario

### 2.1 Negocio (Tenant / Business)

Empresa o emprendedor que se registra en la plataforma para publicar su catálogo y recibir pedidos. Puede ser restaurante, tienda, proveedor de servicios o cualquier negocio con catálogo.

Necesidades:
- Publicar y administrar su catálogo sin depender de un desarrollador.
- Recibir pedidos de distintos canales en un solo lugar.
- Configurar reglas, promociones y disponibilidad.
- Visualizar métricas básicas de operación.

### 2.2 Cliente final

Persona que consulta el catálogo y realiza un pedido a través de cualquier canal disponible.

Necesidades:
- Encontrar lo que busca rápidamente.
- Hacer un pedido sin fricción (sin instalaciones ni registros obligatorios).
- Recibir confirmación de su pedido.

### 2.3 Plataforma (Super-admin)

Equipo interno que administra tenants, monitorea la operación y gestiona la infraestructura multi-tenant.

(**Confirmado** — documento fuente §2, §5)

---

## 3. MVP — Alcance mínimo viable

### 3.1 Justificación del alcance

El mercado de comercio conversacional está dominado por soluciones costosas o de difícil integración para negocios pequeños. El diferenciador inicial es la **combinación de canal QR + WhatsApp con un agente conversacional de texto**, que permite validar el producto con el mínimo de infraestructura y costo.

El canal de voz (llamadas telefónicas) es técnicamente el más costoso y complejo; se valida el producto sin él. (**Decisión propuesta** — ver § [04-decisiones.md §1](04-decisiones.md))

### 3.2 Funcionalidades incluidas en el MVP

| # | Funcionalidad | Justificación |
|---|---|---|
| 1 | Registro y onboarding de negocio (tenant) | Indispensable para multi-tenancy |
| 2 | Gestión de catálogo: categorías, productos, variantes | Núcleo del dominio |
| 3 | Disponibilidad de productos (activo/inactivo) | Condición de las reglas del agente |
| 4 | Canal QR: vista del catálogo y creación de pedido | Canal de menor costo y fricción |
| 5 | Canal WhatsApp: agente conversacional de texto | Canal con mayor adopción en México/LATAM |
| 6 | Agente conversacional (texto): interpreta intención, consulta catálogo, crea pedido | Diferenciador principal |
| 7 | Ciclo de vida básico del pedido (creado → confirmado → completado / cancelado) | Operación mínima funcional |
| 8 | Dashboard del negocio: catálogo + seguimiento de pedidos | Cierra el ciclo para el negocio |
| 9 | Multi-tenancy: aislamiento por tenant_id en base compartida | Requisito arquitectónico desde el inicio |
| 10 | Acceso de cliente sin registro obligatorio (QR) | Reduce fricción en canal físico |
| 11 | Identidad por número de teléfono en WhatsApp (sin login adicional) | El canal provee identidad natural |

(**Decisión propuesta** — alcance sujeto a confirmación del dueño del proyecto)

### 3.3 Costo y esfuerzo estimados

> Todas las cifras son supuestos; deben validarse con cotizaciones reales.

| Componente | Costo mensual estimado | Notas |
|---|---|---|
| Infraestructura cloud (servidor + BD) | USD 50–150 **[VERIFICAR]** | Tier mínimo, 1 región |
| WhatsApp Business API (proveedor) | USD 0–50 + costo por conversación **[VERIFICAR]** | Twilio, 360dialog, Meta directa |
| LLM para agente conversacional | USD 20–100 **[VERIFICAR]** | Depende de volumen; Claude API o equivalente |
| Dominio y certificados | USD 5–15 **[VERIFICAR]** | Anual prorrateado |
| **Total estimado mensual** | **USD 75–315 [VERIFICAR]** | Sin voz ni pagos |

| Fase | Esfuerzo estimado | Notas |
|---|---|---|
| MVP (Fase 1) | 12–16 semanas-desarrollador **[VERIFICAR]** | Equipo pequeño (2–3 personas) |

### 3.4 Fuera del alcance del MVP

Los siguientes elementos quedan **explícitamente excluidos** del MVP:

- Agente de voz para llamadas telefónicas → Fase 2.
- Pagos integrados → Fase 2 (ver [04-decisiones.md](04-decisiones.md)).
- Promociones y cupones → Fase 2.
- Reglas específicas por cliente → Fase 2.
- Analítica avanzada y métricas → Fase 3.
- Búsqueda/descubrimiento de negocios en la plataforma web → Fase 2.
- Inventario completo → Fase 3.
- Delivery/logística propia → Fase 3+.
- Facturación empresarial → Fuera de alcance indefinido (**Confirmado** — documento fuente §24).
- Automatización avanzada de campañas → Fuera de alcance indefinido.
- Microservicios, read replicas, event streaming → Evolutivo, no prematuramente.

---

## 4. Criterio de éxito del MVP

Un negocio puede:

1. Registrarse y configurar su catálogo sin asistencia técnica.
2. Generar un código QR que sus clientes puedan escanear para ver el catálogo y hacer un pedido.
3. Recibir pedidos desde WhatsApp mediante el agente conversacional.
4. Ver todos sus pedidos en su dashboard.
5. Marcar productos como inactivos y que el agente respete ese estado.

(**Confirmado** — derivado del §29 del documento fuente)
