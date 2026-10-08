# Fases y Roadmap

→ Convenciones: [00-README.md](00-README.md)  
→ Alcance del MVP: [01-vision-y-alcance.md §3](01-vision-y-alcance.md)  
→ Decisiones que afectan las fases: [04-decisiones.md](04-decisiones.md)

Todas las estimaciones de tiempo y costo son **Supuesto [VERIFICAR]** salvo que se indique lo contrario.

---

## Fase 1 — MVP: Núcleo operativo

**Objetivo:** Que un negocio pueda registrarse, publicar su catálogo, recibir pedidos desde QR y WhatsApp mediante un agente conversacional de texto, y visualizarlos en su dashboard. Todo en entorno multi-tenant.

### Entregables

| # | Entregable |
|---|---|
| 1.1 | Registro y onboarding de negocio (tenant + business profile) |
| 1.2 | Gestión de catálogo: categorías, productos, variantes |
| 1.3 | Disponibilidad de productos (activo / inactivo) |
| 1.4 | Generación y gestión de códigos QR |
| 1.5 | Flujo de pedido desde QR (sin login de cliente) |
| 1.6 | Integración WhatsApp Business API (agente de texto) |
| 1.7 | Agente conversacional de texto: interpreta intención, consulta catálogo, crea pedido |
| 1.8 | Ciclo de vida básico del pedido: creado → confirmado → completado / cancelado |
| 1.9 | Dashboard del negocio: catálogo + seguimiento de pedidos + canal de origen |
| 1.10 | Multi-tenancy: aislamiento por tenant_id (base compartida) |
| 1.11 | Configuración de métodos de cumplimiento por negocio |
| 1.12 | Punto de integración de pagos preparado (sin implementar) |

### Criterios de salida

- Un negocio puede completar el onboarding sin asistencia técnica.
- Un cliente puede escanear un QR, ver el catálogo y crear un pedido sin registrarse.
- Un cliente puede iniciar una conversación en WhatsApp y crear un pedido mediante el agente.
- El negocio ve ambos pedidos en su dashboard con el canal de origen.
- El agente respeta la disponibilidad de productos en tiempo real.
- Un producto de Tenant A es invisible para Tenant B.

### Esfuerzo estimado

| Rol | Dedicación estimada |
|---|---|
| Backend (.NET API) | 8–10 semanas-persona **[VERIFICAR]** |
| Frontend (React — Dashboard) | 6–8 semanas-persona **[VERIFICAR]** |
| Agente conversacional / integración LLM | 4–6 semanas-persona **[VERIFICAR]** |
| QA y pruebas de integración | 2–3 semanas-persona **[VERIFICAR]** |
| **Total** | **20–27 semanas-persona [VERIFICAR]** |

Con un equipo de 2–3 personas: **12–16 semanas calendario [VERIFICAR]**.

### Costo estimado (mensual en producción)

→ Ver [01-vision-y-alcance.md §3.3](01-vision-y-alcance.md) para el desglose.

---

## Fase 2 — Canales adicionales y reglas de negocio

**Objetivo:** Ampliar la cobertura de canales, activar el agente de voz y habilitar promociones, cupones y reglas por cliente.

### Entregables

| # | Entregable |
|---|---|
| 2.1 | Canal de voz: agente telefónico (speech-to-text + text-to-speech + LLM) |
| 2.2 | Integración de pagos (Conekta, MercadoPago, Clip u otro proveedor — ver [04-decisiones.md §DEC-004](04-decisiones.md)) |
| 2.3 | Promociones: configuración, vigencia, reglas de elegibilidad |
| 2.4 | Cupones: códigos, límites de uso, restricciones |
| 2.5 | Reglas específicas por cliente y grupos de clientes |
| 2.6 | Canal web: búsqueda/descubrimiento de negocios en la plataforma |
| 2.7 | Notificaciones de confirmación por WhatsApp / SMS |
| 2.8 | Métricas básicas en el dashboard (pedidos por canal, por período) |

### Criterios de salida

- Un cliente puede llamar por teléfono y el agente crea el pedido.
- Un negocio puede cobrar en línea a través de al menos un proveedor de pagos.
- Un negocio puede configurar una promoción activa y el agente la aplica previa validación del backend.
- Un cliente puede ser bloqueado para comprar un producto específico.

### Esfuerzo estimado

10–14 semanas calendario **[VERIFICAR]**, equipo similar al MVP.

---

## Fase 3 — Maduración y escala

**Objetivo:** Añadir analítica, inventario, herramientas de fidelización y preparar la arquitectura para escalar.

### Entregables

| # | Entregable |
|---|---|
| 3.1 | Analítica avanzada: métricas de conversión, comportamiento de clientes, rendimiento de catálogo |
| 3.2 | Inventario básico: stock por producto, alertas de agotamiento |
| 3.3 | Más canales conversacionales (Telegram, Instagram DM, etc.) |
| 3.4 | Automatización básica de campañas (mensajes programados por segmento) |
| 3.5 | Delivery propio: asignación de repartidores, seguimiento de entrega |
| 3.6 | Preparación de migración a microservicios si el volumen lo justifica |

### Criterios de salida

- El negocio puede ver el rendimiento de su catálogo con al menos 3 métricas clave.
- El sistema soporta al menos un nuevo canal conversacional.
- La arquitectura puede escalar horizontalmente sin rediseño del dominio.

### Esfuerzo estimado

12–16 semanas calendario **[VERIFICAR]**.

---

## Vista resumen

```
Fase 1 (MVP)        |===========================| ~12–16 sem. calendario
Fase 2              |                |==============| ~10–14 sem. calendario
Fase 3              |                              |================| ~12–16 sem. calendario
```

> Las fases son secuenciales en los entregables de backend/dominio. Las fases 2 y 3 pueden solaparse parcialmente en componentes independientes.

---

## Arquitectura técnica sugerida para el MVP

> Pendiente de validar contra los documentos de arquitectura frontend (React) y backend (.NET API) cuando estén disponibles. (**Decisión propuesta**)

| Capa | Tecnología sugerida | Notas |
|---|---|---|
| Backend API | .NET (ya definido por el proyecto) | REST o minimal API |
| Base de datos | PostgreSQL o SQL Server con tenant_id | Compartida, aislamiento lógico |
| Frontend / Dashboard | React (ya definido por el proyecto) | SPA o Next.js |
| Agente conversacional | Claude API (Anthropic) o equivalente | Tool-use para consultar catálogo |
| Canal WhatsApp | Meta Business API / 360dialog / Twilio | **[VERIFICAR]** costo y aprobación |
| Canal QR | Generación server-side; landing page web | Sin dependencia de app móvil |
| Canal voz (Fase 2) | Twilio Voice + STT/TTS | Evaluar en Fase 2 |
| Infraestructura | Cloud proveedor a definir (Azure/AWS/GCP) | **[VERIFICAR]** |
