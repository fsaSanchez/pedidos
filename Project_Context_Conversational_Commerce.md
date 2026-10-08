# AI Conversational Commerce Platform

**Project Context** & Master Technical Context

Documento maestro de contexto para desarrollo asistido por IA

Versión: 2.0.0

Fecha: 3 de octubre de 2026

Estado: Diseño / Preparación para implementación

# 1. Propósito del documento

Este documento concentra el contexto técnico, funcional y arquitectónico acordado para el proyecto. Su objetivo es servir como referencia maestra para el desarrollo del SaaS y, especialmente, como contexto base para herramientas de IA como Claude.

El proyecto debe construirse de forma incremental, manteniendo el dominio como fuente de verdad y evitando que la IA introduzca reglas, arquitectura o conceptos no aprobados.

Este documento complementa, pero no sustituye, los documentos detallados ubicados en docs/ y los prompts operativos ubicados en ai/.

# 2. Visión del producto

El producto será una plataforma SaaS multi-tenant de comercio conversacional y gestión de pedidos para múltiples tipos de negocios. No estará limitada a restaurantes.

La plataforma permitirá que un negocio se registre, configure su información comercial, publique su catálogo de productos o servicios y reciba pedidos o solicitudes desde distintos canales.

El componente diferencial será un agente conversacional basado en IA capaz de atender llamadas y otros canales, entender lo que desea el cliente, consultar el catálogo y las reglas vigentes del negocio, guiar la conversación y registrar la operación mediante APIs del backend.

La IA no será la autoridad de negocio. El backend será siempre responsable de validar disponibilidad, precios, promociones, restricciones y cualquier otra regla.

## 2.1 Tipos de negocio

El modelo debe ser genérico y permitir diferentes verticales. Un restaurante es solamente uno de los posibles casos de uso.

- Restaurantes y establecimientos de alimentos.
- Tiendas y comercios.
- Negocios que venden productos.
- Negocios que ofrecen servicios.
- Otros negocios que requieran catálogo, atención conversacional y pedidos o solicitudes.

Por esta razón, los conceptos del dominio deben evitar asumir mesas, cocina, ingredientes o restaurantes salvo que una capacidad específica los requiera.

## 2.2 Problema que resuelve

- Reduce la dependencia de un usuario humano para tomar pedidos.
- Permite atender clientes por teléfono y canales digitales.
- Permite que el cliente consulte productos y opciones sin esperar a un empleado.
- Permite mantener actualizado el catálogo y la disponibilidad.
- Centraliza pedidos provenientes de diferentes canales.
- Permite aplicar promociones, cupones y reglas específicas.
- Proporciona al negocio un dashboard para administrar su operación.

# 3. Experiencia principal del cliente

## 3.1 Llamada telefónica

1. El cliente llama al negocio.
2. El agente de voz identifica la intención.
3. Consulta el catálogo y el estado actual de los productos.
4. Guía al cliente para seleccionar productos, variantes, modificadores o servicios.
5. Aplica únicamente promociones o reglas que el backend autorice.
6. Confirma el pedido con el cliente.
7. El backend crea y confirma la orden.
8. El sistema puede enviar el resumen del pedido por WhatsApp.
9. El negocio visualiza el pedido en su dashboard.

## 3.2 WhatsApp

10. El cliente inicia una conversación.
11. El agente identifica la intención.
12. Consulta el catálogo.
13. Resuelve preguntas y guía al cliente.
14. Construye el pedido.
15. Valida reglas y promociones mediante backend.
16. Confirma el pedido.
17. El negocio recibe la orden en su dashboard.

## 3.3 QR

18. El negocio genera un QR.
19. El QR puede colocarse en mesas, mostradores, espacios físicos o cualquier ubicación que el negocio considere conveniente.
20. El cliente escanea el QR.
21. Visualiza el menú o catálogo.
22. Puede crear un pedido.
23. El cliente puede elegir el destino del pedido: consumo local, para llevar, envío u otra modalidad soportada por el negocio.
24. El pedido queda registrado en el sistema.

## 3.4 Búsqueda del negocio

El cliente también podrá encontrar un negocio dentro de la plataforma sin encontrarse físicamente en el establecimiento. Desde allí podrá consultar el catálogo y, cuando el negocio lo permita, iniciar un pedido.

El sistema debe evitar asumir que un pedido originado desde búsqueda siempre es envío o para llevar. El método de cumplimiento se determina según las opciones y reglas disponibles para ese negocio.

# 4. Canales

| Canal | Propósito |
| --- | --- |
| Phone / Voice Agent | Atención telefónica y creación conversacional de pedidos. |
| WhatsApp | Conversación digital, consulta y creación de pedidos. |
| QR | Acceso directo al catálogo y creación de pedidos desde una ubicación física. |
| Web | Búsqueda de negocios, consulta de catálogo y pedidos. |

Los canales son interfaces distintas sobre un mismo dominio. No deben generar modelos de negocio independientes.

# 5. Dashboard del negocio

Cada negocio contará con un dashboard administrativo para operar su información.

- Gestión del catálogo.
- Alta, modificación e inactivación de productos.
- Gestión de categorías.
- Variantes y modificadores cuando apliquen.
- Consulta y seguimiento de pedidos.
- Visualización del canal de origen.
- Configuración de métodos de cumplimiento.
- Configuración de promociones.
- Creación y administración de cupones.
- Configuración de reglas para clientes.
- Generación y administración de códigos QR.
- Configuración del agente conversacional.
- Información y métricas futuras.

# 6. Disponibilidad de productos y servicios

Un negocio podrá marcar productos o servicios como inactivos o no disponibles. El agente debe consultar siempre el estado actual antes de ofrecer o aceptar una selección.

Ejemplo: si un producto está temporalmente inactivo por falta de ingredientes, el agente no debe ofrecerlo como disponible.

La regla debe ser aplicada por el backend. El frontend y el agente únicamente consumen el estado autorizado.

# 7. Pedidos

El Order es uno de los conceptos centrales del dominio.

## 7.1 Características

- Puede originarse desde múltiples canales.
- Pertenece a un Tenant y a un Business.
- Está asociado a un cliente cuando la identidad del cliente está disponible.
- Tiene un estado de ciclo de vida.
- Tiene un canal de origen.
- Tiene un método de cumplimiento.
- Contiene elementos del pedido.
- Conserva el precio aplicado en el momento de la operación.
- Debe validarse antes de confirmarse.
- Después de confirmarse, no debe depender de los datos vivos del catálogo para reconstruir su importe histórico.

## 7.2 Métodos de cumplimiento

El modelo no debe acoplarse a un único método. Un pedido puede ser configurado según las capacidades del negocio.

- Consumo local.
- Para llevar.
- Envío.
- Otros métodos que posteriormente se incorporen.

Un mismo canal puede originar distintos métodos. Por ejemplo, un cliente puede llamar y pedir para comer en el local, o escanear un QR y pedir para llevar.

# 8. Promociones y cupones

Los negocios podrán configurar promociones y cupones.

- Promociones con vigencia.
- Cupones con códigos.
- Límites de uso.
- Reglas de elegibilidad.
- Restricciones por cliente.
- Restricciones por producto, categoría o pedido cuando el dominio lo permita.

La IA nunca debe decidir por sí misma si un cupón es válido. Debe solicitar al backend la validación y comunicar el resultado.

# 9. Reglas específicas por cliente

Una capacidad importante del producto será permitir que un negocio configure reglas que determinen qué puede o no puede vender a determinados clientes o grupos de clientes.

Estas reglas deben ser modeladas explícitamente y evaluadas por el backend.

- Elegibilidad para productos o servicios.
- Elegibilidad para promociones.
- Restricciones de compra.
- Condiciones específicas de un cliente.
- Reglas futuras de segmentación.

La IA puede preguntar, explicar y guiar, pero nunca sustituir el motor de reglas.

# 10. Agente conversacional

## 10.1 Responsabilidades

- Interpretar intención.
- Entender lenguaje natural.
- Mantener contexto conversacional.
- Consultar catálogo mediante herramientas.
- Solicitar validaciones al backend.
- Guiar al cliente.
- Sugerir opciones disponibles.
- Solicitar confirmación antes de acciones relevantes.
- Presentar resultados al cliente.

## 10.2 Lo que NO puede hacer

- No puede inventar precios.
- No puede decidir disponibilidad.
- No puede aplicar descuentos arbitrarios.
- No puede saltarse restricciones.
- No puede confirmar una operación que el backend rechace.
- No puede modificar directamente la base de datos.
- No puede contener reglas de negocio como fuente primaria.

Principio central: AI is an orchestration layer, not the business authority.

# 11. Arquitectura general

Customer\
↓\
Channel\
↓\
Conversation / Interaction Layer\
↓\
Application Layer\
↓\
Domain Layer\
↓\
Infrastructure Layer\
↓\
SQL Server / External Services

La arquitectura inicial será un Modular Monolith. La separación modular y los límites de dominio deben permitir extraer posteriormente bounded contexts a servicios independientes si el crecimiento lo requiere.

## 11.1 Tecnologías principales

| Área | Tecnología / estrategia |
| --- | --- |
| Backend | .NET / ASP.NET Core |
| Arquitectura | DDD + Clean Architecture |
| Persistencia | SQL Server + EF Core |
| Frontend | React + TypeScript |
| IA de desarrollo | Claude |
| IA conversacional | LLM / agente conversacional mediante herramientas del backend |
| Mensajería | WhatsApp y otros proveedores mediante infraestructura desacoplada |
| Eventos | Domain Events + Outbox Pattern |

# 12. Domain-Driven Design

El dominio es la fuente de verdad del sistema.

Las decisiones de arquitectura deben derivarse del modelo de negocio, no de las tablas ni de la interfaz.

- Aggregates protegen invariantes.
- Entities representan identidad.
- Value Objects representan conceptos sin identidad propia.
- Domain Events representan hechos que ya ocurrieron.
- Bounded Contexts evitan acoplamiento excesivo.
- Ubiquitous Language mantiene consistencia entre negocio, código y documentación.

# 13. Bounded Contexts iniciales

Los límites exactos deben respetar los documentos de dominio, pero conceptualmente el sistema contempla áreas como:

- Tenant / Business Management.
- Catalog.
- Customer.
- Order.
- Promotion / Coupon.
- Conversation.
- Notification / Channel Integration.
- Rules / Eligibility.

No se debe asumir que cada área necesariamente será un microservicio. Inicialmente forman parte de un Modular Monolith.

# 14. Multi-tenancy

El sistema es multi-tenant desde su diseño inicial.

- Cada Tenant representa un cliente SaaS.
- Un Tenant puede administrar uno o varios Businesses.
- Los datos deben quedar aislados entre tenants.
- Las operaciones deben validar el contexto de Tenant.
- Las consultas no deben permitir acceso cruzado entre tenants.

TenantId debe formar parte de la estrategia de persistencia y de las validaciones de acceso. BusinessId se utiliza cuando el dato pertenece específicamente a un negocio.

# 15. Persistencia

La base de datos es una representación de persistencia del dominio, no el dominio mismo.

La estrategia inicial es SQL Server con EF Core.

El esquema contempla, entre otras, entidades de persistencia para:

- Tenants.
- Businesses.
- Catalogs.
- Products.
- Categories.
- ProductVariants.
- ModifierGroups.
- Modifiers.
- Customers.
- Orders.
- OrderItems.
- Promotions.
- Coupons.
- Conversations.
- Messages.
- DomainEvents.
- OutboxMessages.

## 15.1 Reglas de persistencia

- TenantId debe formar parte del aislamiento.
- Los precios de una orden deben conservarse como snapshot.
- Las órdenes confirmadas no deben depender del precio actual del producto.
- Los Domain Events son append-only.
- Outbox se utiliza para evitar pérdida de eventos al integrar persistencia y publicación.
- EF Core no debe permitir que Infrastructure contamine Domain.

# 16. Eventos

El sistema adopta pensamiento orientado a eventos para representar cambios relevantes del dominio y permitir integraciones confiables.

Ejemplos conceptuales:

- OrderDraftCreated.
- OrderItemAdded.
- OrderConfirmed.
- OrderRejected.
- ProductUpdated.
- PromotionApplied.
- ConversationUpdated.

Los eventos deben ser inmutables y representar hechos del dominio.

# 17. Outbox Pattern

Cuando una operación de negocio cambia el estado y requiere publicar un evento o enviar una notificación, el sistema debe evitar el problema de una transacción de base de datos exitosa seguida de una publicación fallida.

La estrategia de Outbox permite persistir el mensaje dentro de la misma transacción y procesarlo posteriormente.

# 18. Documentación del proyecto

docs/\
├── 00-product-vision.md\
├── 01-ubiquitous-language.md\
├── 02-domain-architecture.md\
├── 03-business-rules.md\
├── 04-event-storming.md\
├── 05-domain-model.md\
├── 06-functional-specifications.md\
├── 07-engineering-standards.md\
├── 08-persistence-model.md\
└── 09-database-schema.sql\

README.md\

ai/\
├── backend.md\
├── frontend.md\
├── feature.md\
├── review.md\
└── bugfix.md

## 18.1 Propósito de los documentos

| Documento | Responsabilidad |
| --- | --- |
| 00-product-vision | Qué es el producto y por qué existe. |
| 01-ubiquitous-language | Lenguaje común del dominio. |
| 02-domain-architecture | Bounded contexts y estructura de dominio. |
| 03-business-rules | Reglas obligatorias del negocio. |
| 04-event-storming | Eventos, comandos y flujos del dominio. |
| 05-domain-model | Aggregates, entities y value objects. |
| 06-functional-specifications | Comportamiento funcional. |
| 07-engineering-standards | Estándares técnicos y de código. |
| 08-persistence-model | Mapeo del dominio a persistencia. |
| 09-database-schema.sql | Snapshot físico del esquema SQL. |

# 19. Sistema de desarrollo asistido por Claude

El desarrollo se realizará inicialmente con Claude en modalidad manual, debido a que el usuario utilizará por ahora un plan gratuito y no depende de automatización vía API.

Los archivos de ai/ funcionan como prompts maestros reutilizables.

## 19.1 Prompts maestros

| Prompt | Propósito |
| --- | --- |
| feature.md | Diseñar una funcionalidad completa desde el dominio hasta backend/frontend. |
| backend.md | Generar backend respetando DDD, Clean Architecture y reglas. |
| frontend.md | Generar frontend alineado al backend y al dominio. |
| review.md | Auditar arquitectura, dominio, seguridad multi-tenant y calidad. |
| bugfix.md | Diagnosticar y corregir bugs sin parches arquitectónicamente peligrosos. |

# 20. Pipeline manual con Claude

Feature Request\
↓\
feature.md\
↓\
backend.md\
↓\
frontend.md\
↓\
review.md\
↓\
bugfix.md (cuando sea necesario)\
↓\
Implementation / Validation

El objetivo es que cada etapa reciba el contexto necesario de la etapa anterior y que el dominio siga siendo la autoridad.

Los outputs deben guardarse como artefactos versionables cuando sean útiles para continuar una feature.

# 21. CLI local de desarrollo asistido por IA

Se planea construir un CLI local para reducir el trabajo manual de preparar y encadenar prompts.

En la primera etapa, el CLI NO necesita llamar a Claude. Su función será preparar automáticamente los prompts y el contexto para que el desarrollador los copie y pegue en Claude.

ai-engine/\
├── src/\
│   ├── index.ts\
│   ├── commands/\
│   │   ├── run.ts\
│   │   ├── feature.ts\
│   │   ├── backend.ts\
│   │   ├── frontend.ts\
│   │   ├── review.ts\
│   │   └── bugfix.ts\
│   ├── core/\
│   │   ├── promptLoader.ts\
│   │   ├── contextBuilder.ts\
│   │   └── pipeline.ts\
│   └── prompts/\
├── templates/\
├── outputs/\
├── package.json\
└── tsconfig.json

El stack recomendado para el CLI es Node.js + TypeScript por su facilidad para manipular archivos, construir comandos y posteriormente integrar una API de LLM si se desea.

## 21.1 Evolución del CLI

- Fase 1: generación local de prompts.
- Fase 2: gestión de contexto y artefactos.
- Fase 3: integración opcional con API de Claude.
- Fase 4: pipeline automatizada con validaciones y reintentos.

La arquitectura debe permitir evolucionar sin cambiar los documentos de dominio ni los prompts maestros.

# 22. Feature inicial de referencia: Order Creation Engine

La primera feature real propuesta es la creación de pedidos omnicanal.

Es una buena primera feature porque conecta catálogo, cliente, conversación, reglas, órdenes, canales y dashboard.

## 22.1 Flujo conceptual

ConversationStarted\
↓\
IntentRecognized\
↓\
CatalogRequested\
↓\
ProductSelected\
↓\
OrderDraftCreated\
↓\
OrderValidated\
↓\
OrderConfirmed\
↓\
OrderNotificationSent

## 22.2 Casos que debe soportar

- Pedido iniciado por WhatsApp.
- Pedido originado por llamada.
- Pedido iniciado desde QR.
- Pedido iniciado desde web.
- Selección de productos disponibles.
- Productos temporalmente inactivos.
- Variantes y modificadores.
- Promociones y cupones.
- Restricciones por cliente.
- Métodos de cumplimiento distintos.
- Notificación/resumen posterior.

# 23. Principios de ingeniería

- Domain First.
- Backend as Source of Truth.
- No Business Logic in AI.
- No Business Logic in Frontend.
- Strict Tenant Isolation.
- No duplicated business rules.
- Explicit aggregate boundaries.
- Events for meaningful domain changes.
- Testable deterministic behavior.
- Configuration over hardcoded business assumptions.
- API contracts must be explicit.
- Infrastructure must not leak into Domain.

# 24. Qué NO forma parte del alcance actual

La facturación empresarial no forma parte del alcance inicial.

El producto está pensado inicialmente para negocios que atienden clientes consumidores finales y no requieren facturación empresarial como parte del flujo principal.

Esto no impide que la arquitectura pueda incorporar facturación en el futuro, pero no debe introducirse ahora como dependencia del dominio principal.

# 25. Aspectos futuros

El sistema debe dejar espacio para evolucionar, sin implementar prematuramente todo lo siguiente.

- Pagos integrados.
- Analítica avanzada.
- Inventario completo.
- Delivery propio.
- Más canales conversacionales.
- Automatización avanzada de campañas.
- Microservicios por bounded context.
- Read replicas.
- Event streaming.
- Facturación, si el producto posteriormente la requiere.

# 26. Regla de oro para Claude

Claude debe tratar la documentación del proyecto como contrato arquitectónico.

Ante una ambigüedad, no debe inventar una regla de negocio. Debe identificar la ambigüedad y solicitar aclaración o proponer alternativas explícitas antes de modificar el dominio.

La IA puede ayudar a diseñar e implementar, pero la autoridad final sobre el comportamiento del sistema es el dominio definido por el proyecto.

# 27. Estado actual del proyecto

| Área | Estado |
| --- | --- |
| Visión de producto | Definida |
| Lenguaje ubicuo | Definido |
| Arquitectura de dominio | Definida |
| Reglas de negocio | Definidas |
| Event Storming | Definido |
| Domain Model | Definido |
| Especificaciones funcionales | Definidas |
| Engineering Standards | Definidos |
| Persistence Model | Definido |
| Database Schema | Definido como primera versión |
| AI prompts | Definidos |
| CLI local | Diseño inicial; implementación pendiente |
| Implementación del producto | Pendiente / siguiente fase |

# 28. Próximo camino recomendado

25. Revisar y congelar el dominio antes de comenzar a generar código.
26. Construir el repositorio base con backend .NET y frontend React.
27. Configurar SQL Server y EF Core.
28. Crear el primer bounded context con pruebas.
29. Implementar Order Creation Engine por etapas.
30. Integrar catálogo y disponibilidad.
31. Agregar dashboard de pedidos.
32. Agregar promociones y cupones.
33. Integrar canales conversacionales.
34. Construir y utilizar el CLI local para estandarizar el desarrollo asistido por Claude.
35. Ejecutar review antes de cerrar cada feature.

# 29. Criterio de éxito

El proyecto será exitoso si un negocio puede registrarse, configurar su catálogo, definir reglas y disponibilidad, recibir pedidos desde distintos canales, permitir que un agente conversacional atienda al cliente y visualizar las operaciones desde su dashboard, todo dentro de un entorno multi-tenant seguro y con reglas de negocio centralizadas en el backend.

# 30. Resumen ejecutivo

La plataforma es un SaaS multi-tenant de comercio conversacional para múltiples tipos de negocios. Su núcleo combina catálogo, clientes, reglas, promociones, pedidos y conversaciones, exponiéndolos a través de canales como llamadas, WhatsApp, QR y web.

La arquitectura utilizará .NET, React, SQL Server y DDD/Clean Architecture, comenzando como Modular Monolith. El backend será la autoridad de negocio; la IA será una capa de interpretación y orquestación.

El desarrollo asistido por Claude estará gobernado por documentación y cinco prompts maestros: feature, backend, frontend, review y bugfix. Inicialmente el flujo será manual por las limitaciones del plan gratuito, y se complementará con un CLI local que prepare y organice los prompts. Posteriormente podrá evolucionar a ejecución automática mediante API sin modificar el dominio ni la estructura documental.
