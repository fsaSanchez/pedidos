# Documentación del Proyecto — AI Conversational Commerce Platform

## Propósito de esta carpeta

Estos documentos son la **memoria estructurada y guía de referencia** del proyecto. NO modifican ni reemplazan el documento fuente (`Project_Context_Conversational_Commerce.md`); lo complementan con análisis, decisiones y estructura de trabajo.

Toda decisión de diseño vive aquí, no en el archivo fuente ni en el chat.

---

## Índice

| Archivo | Contenido |
|---|---|
| [00-README.md](00-README.md) | Este índice y convenciones |
| [01-vision-y-alcance.md](01-vision-y-alcance.md) | Problema, usuarios, MVP y fuera de alcance |
| [02-reglas-de-negocio.md](02-reglas-de-negocio.md) | Reglas consolidadas, sin contradicciones, con changelog |
| [03-fases-y-roadmap.md](03-fases-y-roadmap.md) | Fases, entregables, criterios de salida, esfuerzo y costo |
| [04-decisiones.md](04-decisiones.md) | Registro de decisiones clave con contexto, alternativas y estado |
| [05-pendientes-y-riesgos.md](05-pendientes-y-riesgos.md) | Preguntas abiertas para el dueño del proyecto y riesgos |

---

## Convenciones de etiquetas

Cada afirmación en estos documentos lleva una de estas etiquetas cuando su certeza importa:

| Etiqueta | Significado |
|---|---|
| **Confirmado** | Acordado explícitamente en el documento fuente o por el dueño del proyecto. |
| **Decisión propuesta** | Recomendación del análisis; pendiente de confirmación por el dueño del proyecto. |
| **Supuesto \[VERIFICAR\]** | Estimación o hipótesis razonable; debe validarse con datos reales o cotizaciones. |

Las etiquetas se colocan entre paréntesis al final de la afirmación o en la columna "Estado" de las tablas de decisiones.

---

## Regla de referencia cruzada

Cada dato vive en **un solo documento**. Los demás lo referencian con la notación `→ [doc](ruta.md)`.  
No se duplica contenido entre archivos.

---

## Cómo actualizar estos documentos

1. Nunca modificar el archivo fuente original.
2. Actualizar el documento correspondiente y registrar el cambio en el changelog de `02-reglas-de-negocio.md` si afecta reglas de negocio, o en el log de `04-decisiones.md` si es una decisión de diseño.
3. Cambiar la etiqueta de "Decisión propuesta" a "Confirmado" solo cuando el dueño del proyecto lo valide explícitamente.
4. Cambiar "Supuesto [VERIFICAR]" a "Confirmado" solo cuando se cuente con datos reales.

---

## Estado del documento

- Última actualización: 2026-10-07
- Basado en: `Project_Context_Conversational_Commerce.md` v2.0.0 (3 oct 2026)
- Elaborado por: Análisis asistido por IA (Claude Sonnet 4.6)
