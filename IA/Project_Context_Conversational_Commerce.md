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

#

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



# 29. Criterio de éxito

El proyecto será exitoso si un negocio puede registrarse, configurar su catálogo, definir reglas y disponibilidad, recibir pedidos desde distintos canales, permitir que un agente conversacional atienda al cliente y visualizar las operaciones desde su dashboard, todo dentro de un entorno multi-tenant seguro y con reglas de negocio centralizadas en el backend.

# 30. Resumen ejecutivo

La plataforma es un SaaS multi-tenant de comercio conversacional para múltiples tipos de negocios. Su núcleo combina catálogo, clientes, reglas, promociones, pedidos y conversaciones, exponiéndolos a través de canales como llamadas, WhatsApp, QR y web.


El desarrollo asistido por Claude estará gobernado por documentación y cinco prompts maestros: feature, backend, frontend, review y bugfix. Inicialmente el flujo será manual por las limitaciones del plan gratuito, y se complementará con un CLI local que prepare y organice los prompts. Posteriormente podrá evolucionar a ejecución automática mediante API sin modificar el dominio ni la estructura documental.
