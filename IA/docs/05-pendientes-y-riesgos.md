# Pendientes y Riesgos

→ Convenciones: [00-README.md](00-README.md)

---

## 1. Preguntas abiertas para el dueño del proyecto

Las siguientes preguntas requieren respuesta antes de avanzar en las áreas indicadas.

| # | Pregunta | Área afectada | Prioridad |
|---|---|---|---|
| P-01 | ¿Cuándo se incorporarán las secciones §11–§21, §23, §27–§28 al documento fuente principal? Pueden contener reglas de autenticación, modelo de datos o arquitectura que afecten el diseño actual. | Arquitectura general | Alta |
| P-02 | ¿Cuál será el proveedor de WhatsApp Business API? (Meta directa, 360dialog, Twilio, otro) Afecta costos, tiempo de aprobación y formato de integración. | Fase 1 — Canal WhatsApp | Alta |
| P-03 | ¿Cuál es el proveedor de LLM para el agente conversacional? (Claude API, OpenAI, otro) Afecta costo por token, límites de contexto y capacidades de tool-use. | Fase 1 — Agente | Alta |
| P-04 | ¿Cuál es el proveedor de infraestructura cloud? (Azure, AWS, GCP, otro) El equipo puede tener preferencia o créditos disponibles. | Fase 1 — Infraestructura | Alta |
| P-05 | ¿El sistema debe cumplir con la Ley Federal de Protección de Datos Personales en Posesión de Particulares (LFPDPPP) de México desde el MVP? ¿Hay requerimientos de GDPR si operará en otros países? | Multi-tenancy, cliente | Alta |
| P-06 | ¿Cómo se facturará a los negocios-clientes (tenants)? ¿Por pedido, por negocio, por plan mensual, por uso de agente? Esto no es facturación empresarial del §24; es el modelo de negocio de la plataforma. | Modelo de negocio | Alta |
| P-07 | ¿Qué idiomas debe soportar el agente conversacional en el MVP? ¿Solo español o también inglés u otros? | Fase 1 — Agente | Media |
| P-08 | ¿El negocio puede tener múltiples usuarios (empleados) con roles distintos en su dashboard? ¿O el MVP asume un solo administrador por negocio? | Fase 1 — Dashboard | Media |
| P-09 | ¿Qué canal de notificación usa el negocio cuando llega un nuevo pedido? ¿Solo el dashboard, o también email, SMS o push? | Fase 1 — Pedidos | Media |
| P-10 | ¿Los documentos de arquitectura frontend (React) y backend (.NET API) ya están disponibles? Están referenciados en el documento fuente (§30) pero no se han compartido. | Arquitectura — Fases 1–3 | Alta |

---

## 2. Riesgos principales

| # | Riesgo | Probabilidad | Impacto | Mitigación |
|---|---|---|---|---|
| R-01 | **Aprobación de WhatsApp Business API demorada.** Meta puede tardar días o semanas en aprobar una cuenta Business. Si el canal WhatsApp es crítico para el MVP, un retraso bloquea la entrega. | Media | Alto | Iniciar el proceso de solicitud desde el inicio del desarrollo. Tener QR como canal alternativo funcional en el MVP. |
| R-02 | **Alucinaciones del agente conversacional.** El LLM podría inventar precios, disponibilidad o productos que no existen. | Media | Alto | El agente solo consulta catálogo y disponibilidad vía API del backend; nunca responde con datos que no vengan del backend. Implementar pruebas de adversarial prompting antes del lanzamiento. |
| R-03 | **Filtración de datos entre tenants.** Un error en las consultas podría exponer pedidos o catálogos de un tenant a otro. | Baja | Crítico | Todos los queries deben filtrar por tenant_id. Tests de aislamiento son criterio de salida de Fase 1. Revisión de código obligatoria para toda query de datos. |
| R-04 | **Scope creep en el MVP.** La presión por incluir el agente de voz, pagos o promociones antes de validar el producto base puede extender el MVP indefinidamente. | Alta | Medio | Mantener el backlog priorizado. Las funcionalidades fuera del MVP están explícitamente listadas en [01-vision-y-alcance.md §3.4](01-vision-y-alcance.md). Cualquier adición al MVP requiere aprobación explícita del dueño del proyecto. |
| R-05 | **Costo del LLM más alto de lo estimado.** Si las conversaciones son largas o el volumen crece rápido, el costo por token puede dispararse. | Media | Medio | Implementar caché de respuestas frecuentes. Truncar el contexto conversacional a una ventana razonable. Monitorear consumo de tokens desde el inicio. |
| R-06 | **Secciones §11–§21 del documento fuente contienen reglas que contradicen el diseño actual.** Al incorporarlas, puede ser necesario rediseñar partes del dominio. | Media | Alto | Incorporar esas secciones lo antes posible (→ P-01). Diseñar con interfaces limpias entre capas para minimizar el costo del rediseño. |
| R-07 | **El negocio no completa el onboarding sin asistencia.** Si el proceso es demasiado complejo, aumenta el costo de soporte. | Media | Medio | Diseñar el onboarding con el menor número de pasos posible. Incluir valores predeterminados sensatos. Agregar guía contextual en el dashboard. |
| R-08 | **Pedidos falsos o spam en canal QR.** Sin autenticación, cualquier persona puede crear pedidos. | Media | Medio | Implementar rate limiting por token de sesión y por QR. El negocio puede confirmar o rechazar pedidos desde el dashboard antes de prepararlos. |

---

## 3. Dependencias externas críticas

| Dependencia | Descripción | Responsable de resolución |
|---|---|---|
| Documentos de arquitectura frontend y backend | Sin ellos, la propuesta de arquitectura en [03-fases-y-roadmap.md](03-fases-y-roadmap.md) es provisional. | Dueño del proyecto |
| Aprobación WhatsApp Business API | Proceso de Meta; puede demorar. | Equipo de proyecto (iniciar pronto) |
| Secciones §11–§21, §23, §27–§28 | Pueden contener reglas de dominio no modeladas aún. | Dueño del proyecto |
| Elección de proveedor de pagos | Necesaria antes de iniciar Fase 2. | Dueño del proyecto |
| Definición del modelo de negocio (facturación a tenants) | Afecta la arquitectura de registro y onboarding. | Dueño del proyecto |
