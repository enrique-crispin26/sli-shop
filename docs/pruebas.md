# Pruebas QA — AlisonShop

## 1. Objetivo

Verificar el funcionamiento de la lógica de búsqueda de productos implementada en JavaScript dentro del workflow de n8n.

Las pruebas permiten comprobar el procesamiento de consultas, la aplicación de filtros, la validación de existencias y la generación de alternativas.

## 2. Herramientas utilizadas

- JavaScript.
- Node.js.
- Módulo `node:test`.
- Módulo `node:assert/strict`.
- n8n.
- JSON.

## 3. Alcance de las pruebas

Las pruebas unitarias se realizan sobre el código JavaScript del nodo `Code in JavaScript`, utilizando un inventario simulado.

Se comprueban los siguientes comportamientos:

- Búsqueda de celulares por modelo.
- Búsqueda sin distinguir mayúsculas y minúsculas.
- Filtrado por presupuesto.
- Validación de existencias.
- Exclusión de productos con precios inválidos.
- Clasificación de resultados.
- Generación de alternativas.
- Ordenamiento y limitación de resultados.
- Normalización de datos.

Estas pruebas no requieren conexiones reales con WhatsApp, Google Sheets u OpenAI.

## 4. Casos de prueba

| ID | Caso de prueba | Resultado esperado |
|---|---|---|
| TC-01 | Buscar celular por modelo | Identificar el modelo solicitado. |
| TC-02 | Buscar utilizando mayúsculas | Encontrar productos sin distinguir mayúsculas. |
| TC-03 | Consultar con presupuesto limitado | Excluir productos que superen el presupuesto. |
| TC-04 | Consultar sin presupuesto máximo | Mostrar productos disponibles sin aplicar un límite de precio. |
| TC-05 | Buscar productos sin stock | Excluir productos sin existencias. |
| TC-06 | Buscar productos con precio cero | Excluir productos con precio inválido. |
| TC-07 | Consultar un modelo inexistente | Clasificar el resultado como `solo_alternativas` cuando existan alternativas. |
| TC-08 | Consultar con presupuesto insuficiente | Clasificar el resultado como `sin_resultados` si no existen productos vendibles. |
| TC-09 | Verificar el orden de resultados | Comprobar el ordenamiento por precio esperado en la prueba. |
| TC-10 | Buscar múltiples coincidencias | Limitar los resultados a tres productos. |
| TC-11 | Obtener múltiples alternativas | Limitar las alternativas a tres productos. |
| TC-12 | Procesar datos con espacios y formatos distintos | Normalizar el nombre, producto y número de contacto. |
| TC-13 | Utilizar la columna Existencias | Reconocerla como alternativa a la columna Stock. |
| TC-14 | Consultar un inventario vacío | Devolver el estado `sin_resultados`. |
| TC-15 | Buscar marca y modelo juntos | Verificar consultas como Samsung A27. Actualmente marcado como pendiente en el archivo de pruebas. |

## 5. Ejecución de pruebas

El archivo que contiene las pruebas automatizadas se encuentra en:

`tests/busqueda.test.cjs`

Para ejecutarlas, se necesita tener Node.js instalado.

Desde la carpeta principal del repositorio se utiliza el comando:

`node --test tests/busqueda.test.cjs`

Node.js ejecuta los casos definidos y muestra sus resultados.

## 6. Registro de resultados

El conjunto actual contiene:

- 14 pruebas automatizadas ejecutables.
- 1 caso identificado como pendiente.
- Pruebas unitarias independientes de los servicios externos.

Los resultados de aprobación o fallo deben registrarse después de ejecutar el comando de pruebas sobre la versión publicada del workflow.

## 7. Limitaciones

Las pruebas automatizadas no comprueban directamente:

- La recepción de mensajes reales de WhatsApp.
- La disponibilidad de la API de Meta.
- La conexión con Google Sheets.
- La generación de respuestas mediante OpenAI.
- El envío efectivo de mensajes al cliente.

Estas funcionalidades requieren pruebas de integración independientes.

## 8. Mejoras previstas

Se contempla ampliar la cobertura de pruebas mediante:

- Validación de consultas combinadas de marca y modelo.
- Pruebas con mensajes vacíos.
- Manejo de errores de servicios externos.
- Validación de respuestas de la inteligencia artificial.
- Pruebas de integración con WhatsApp y Google Sheets.

## 9. Conclusión

La implementación de pruebas automatizadas permite evaluar la lógica comercial del buscador sin depender de las conexiones externas.

Esta metodología facilita la detección de errores, la verificación de reglas de negocio y la identificación de mejoras para futuras versiones de AlisonShop.

El conjunto de pruebas constituye una evidencia práctica de conocimientos en JavaScript, validación de datos y fundamentos de aseguramiento de calidad del software.
