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

 23. Principios de ingeniería

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



