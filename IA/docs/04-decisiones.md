# Registro de Decisiones

→ Convenciones: [00-README.md](00-README.md)

Cada entrada describe una decisión de diseño relevante: contexto, alternativas consideradas, recomendación, costo/esfuerzo y estado.

---

## DEC-001 — Agente de voz para llamadas: ¿MVP o Fase 2?

**Contexto**  
El documento fuente presenta el agente de voz como "componente diferencial" del producto. Sin embargo, el proyecto es nuevo, tiene presupuesto limitado y aún no ha validado el mercado.

**Alternativas consideradas**

| Alternativa | Costo mensual est. | Esfuerzo est. | Riesgo |
|---|---|---|---|
| A) Incluir en MVP | USD 100–400 adicionales **[VERIFICAR]** | +6–8 semanas-persona **[VERIFICAR]** | Alto: voz es difícil de iterar, latencia crítica, UX compleja |
| B) Diferir a Fase 2 | Sin costo adicional en MVP | 0 adicional en MVP | Bajo: se valida el producto por WhatsApp + QR primero |

**Recomendación**  
**Opción B — Diferir a Fase 2.** (**Decisión propuesta**)

Justificación:
- WhatsApp tiene mayor adopción en México/LATAM que las llamadas para pedidos digitales.
- El canal QR + WhatsApp es suficiente para validar el concepto de agente conversacional.
- La infraestructura de voz (Twilio Voice, STT, TTS, manejo de latencia, interrupciones, idioma) agrega complejidad técnica desproporcionada para un MVP con presupuesto limitado.
- Diferirlo no elimina el diferenciador; lo secuencia correctamente.

**Estado:** Decisión propuesta — pendiente de confirmación por el dueño del proyecto.

---

## DEC-002 — Multi-tenancy: modelo de aislamiento de datos

**Contexto**  
La plataforma es multi-tenant desde el inicio. Se debe elegir el modelo de aislamiento que mejor se ajuste al presupuesto y a la capacidad operativa del equipo.

**Alternativas consideradas**

| Alternativa | Descripción | Costo infra | Complejidad operativa | Aislamiento |
|---|---|---|---|---|
| A) Base de datos por tenant | Cada tenant tiene su propia BD | Alto (N bases en producción) | Alta (migraciones x N, backups x N) | Máximo |
| B) Schema por tenant | Una BD, un schema por tenant | Medio | Media | Alto |
| C) Base compartida con tenant_id | Una BD, tenant_id en todas las tablas | Bajo | Baja | Lógico (requiere disciplina en queries) |

**Recomendación**  
**Opción C — Base compartida con tenant_id.** (**Decisión propuesta**)

Justificación:
- Presupuesto limitado: una sola base de datos reduce significativamente el costo de infraestructura y operación.
- El equipo es pequeño: gestionar múltiples bases o schemas multiplica el esfuerzo de migraciones y mantenimiento.
- El aislamiento lógico es suficiente para el MVP y los primeros clientes.
- Si el volumen crece, se puede migrar gradualmente a schemas o bases separadas por tenant; el tenant_id ya presente en todas las tablas facilita esa migración.

**Requisitos de implementación:**
- Todas las entidades del dominio deben incluir `tenant_id` desde el inicio.
- El backend debe filtrar siempre por `tenant_id` en cada consulta; nunca exponer datos entre tenants.
- Los tests de aislamiento deben ser parte del criterio de salida de la Fase 1.

**Estado:** Decisión propuesta — pendiente de confirmación por el dueño del proyecto.

---

## DEC-003 — Acceso del cliente: login vs. QR sin registro vs. esquema híbrido

**Contexto**  
§7.1 indica que un pedido "está asociado a un cliente cuando la identidad está disponible", pero no define cómo se establece esa identidad. El proyecto debe decidir si los clientes se registran, navegan anónimamente o se identifican de forma ligera.

**Alternativas consideradas**

### A) QR sin login (acceso totalmente anónimo)

**Ventajas:**
- Cero fricción para el cliente en canal físico.
- No requiere base de datos de clientes ni gestión de contraseñas.
- Implementación más simple en MVP.

**Riesgos:**
- Imposible historial de pedidos para el cliente.
- Soporte ante incidencias: difícil identificar al cliente sin datos.
- Abuso potencial: pedidos falsos o masivos desde el mismo QR.
- Sin datos de cliente, el negocio no puede hacer seguimiento ni retención.
- No aplica para reglas por cliente (DEC-001 §2.5 de reglas).

### B) Login con OTP (teléfono o correo)

**Ventajas:**
- Identidad verificada, historial de pedidos, soporte efectivo.
- Habilita reglas por cliente y segmentación.
- Menor riesgo de abuso.

**Riesgos:**
- Añade fricción en el flujo de compra.
- Costo de envío de SMS/OTP (USD 0.01–0.05 por mensaje **[VERIFICAR]**).
- Requiere gestión de sesiones y tokens.

### C) Esquema híbrido (recomendado)

| Canal | Identidad | Login |
|---|---|---|
| QR | Sesión anónima con token de corta duración generado por el sistema | No requerido |
| WhatsApp | Número de teléfono como identidad natural provista por la API | No requerido (el canal lo provee) |
| Web | Opcional: teléfono + OTP para historial y reordenar | No obligatorio en MVP |
| Voz (Fase 2) | Caller ID como identidad provisional | No requerido en Fase 2 |

**Protección en canal QR:**
- El token de sesión expira tras completar el pedido o después de N minutos de inactividad.
- El token se vincula a un QR específico (no es reutilizable entre QRs).
- El backend limita pedidos por token y por ventana de tiempo.

**Recomendación**  
**Opción C — Esquema híbrido.** (**Decisión propuesta**)

Justificación:
- Minimiza fricción en el canal de mayor conversión inicial (QR físico).
- WhatsApp ya provee identidad sin costo adicional.
- La autenticación completa puede activarse en Fase 2 cuando el producto esté validado.
- El esquema híbrido es compatible con la regla de "asociar cliente cuando la identidad está disponible" (§7.1).

**Estado:** Decisión propuesta — pendiente de confirmación por el dueño del proyecto.

---

## DEC-004 — Pagos: alcance y preparación

**Contexto**  
§25 clasifica los pagos integrados como aspecto futuro. Sin embargo, el flujo del pedido no menciona el pago, y un pedido "confirmado" en el MVP necesita un estado claro respecto al cobro.

**Decisión para el MVP**  
Los pagos ocurren fuera de la plataforma (en persona, efectivo, transferencia manual, terminal de punto de venta del negocio). (**Decisión propuesta**)

La arquitectura del pedido debe incluir:
- Un campo `payment_status` en la entidad Order (valores: pendiente, pagado, sin pago requerido).
- Un campo `payment_method_type` para registrar el método cuando se implemente.
- Un punto de integración documentado en la API (endpoint reservado pero no funcional en MVP).

**Proveedores a evaluar en Fase 2 (relevantes para México)**

| Proveedor | Relevancia | Métodos soportados |
|---|---|---|
| Conekta | Alta — México, local | Tarjetas, OXXO, SPEI, transferencias **[VERIFICAR]** |
| MercadoPago | Alta — LATAM, popular | Tarjetas, efectivo, SPEI, meses sin intereses **[VERIFICAR]** |
| Clip | Alta — México, PyMEs | Tarjetas en dispositivo físico + links de pago **[VERIFICAR]** |
| Stripe | Media — Internacional | Tarjetas, wallets; soporte México limitado **[VERIFICAR]** |
| OpenPay (BBVA) | Media — México, bancario | Tarjetas, SPEI, OXXO **[VERIFICAR]** |
| PayPal | Baja — México | Amplio reconocimiento pero costos altos **[VERIFICAR]** |

> Todos los proveedores deben verificarse en cuanto a comisiones actuales, tiempos de acreditación y requisitos de cumplimiento para PyMEs en México.

**Estado:** Decisión propuesta — pendiente de confirmación por el dueño del proyecto.

---

## DEC-005 — Método de cumplimiento predeterminado por código QR

**Contexto**  
→ Ver auditoría [02-reglas-de-negocio.md §1.5](02-reglas-de-negocio.md).

Un QR colocado en una mesa de un restaurante implica implícitamente "consumo local", pero el dominio evita asumir el método por el canal.

**Recomendación**  
El negocio puede asignar opcionalmente un método de cumplimiento predeterminado a cada código QR al momento de crearlo. El cliente puede modificarlo si el negocio habilita esa opción. El backend valida que la combinación QR + método sea válida para ese negocio. (**Decisión propuesta**)

**Estado:** Decisión propuesta — pendiente de confirmación por el dueño del proyecto.
