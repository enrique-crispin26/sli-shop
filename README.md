#  AlisonShop — Asistente Comercial con Inteligencia Artificial

### Automatización de consultas comerciales mediante n8n, WhatsApp Business, Google Sheets y OpenAI

##  Descripción

AlisonShop es un proyecto de automatización desarrollado para gestionar consultas comerciales de una tienda de celulares mediante inteligencia artificial.

Su objetivo es facilitar la atención de clientes, automatizando la búsqueda de productos, la verificación de precios y disponibilidad, y la generación de respuestas personalizadas.

El sistema utiliza **n8n** como plataforma de automatización e integra servicios externos mediante APIs y webhooks.

Este proyecto fue desarrollado como prototipo práctico para aplicar conocimientos de programación, automatización de procesos e integración de servicios.

##  Tecnologías utilizadas

| Tecnología            | Función                                             |
| --------------------- | --------------------------------------------------- |
| n8n                   | Orquestación de procesos automatizados              |
| JavaScript            | Procesamiento y validación de información           |
| WhatsApp Business API | Recepción y envío de mensajes                       |
| Google Sheets         | Almacenamiento del catálogo y registro de consultas |
| OpenAI (GPT-5 Nano)   | Generación de respuestas comerciales                |
| Webhooks              | Recepción de eventos HTTP                           |
| JSON                  | Intercambio y estructuración de datos               |

## ⚙️ Funcionamiento del sistema

El proceso comienza cuando un cliente envía una consulta a través de WhatsApp Business.

**Flujo general:**

WhatsApp Business → Webhook POST → Interpretación del mensaje → Validación de datos → Google Sheets → Procesamiento JavaScript → Clasificación → Respuesta automática.

El sistema realiza las siguientes operaciones:

1. Recibe el mensaje mediante un webhook HTTP POST.
2. Extrae la información del cliente y del producto solicitado.
3. Identifica el presupuesto cuando está presente.
4. Consulta el catálogo almacenado en Google Sheets.
5. Filtra productos según precio y existencias.
6. Identifica coincidencias y alternativas disponibles.
7. Clasifica el resultado de la búsqueda.
8. Utiliza OpenAI para redactar respuestas comerciales cuando corresponde.
9. Envía la respuesta mediante la API de WhatsApp.
10. Registra las consultas procesadas en Google Sheets.

## 🔎 Funcionalidades implementadas

### Búsqueda de productos

Permite consultar celulares registrados en el inventario, realizando comparaciones de texto mediante JavaScript.

### Filtrado por presupuesto

Cuando el cliente indica un presupuesto, el sistema filtra los productos que no superan el monto especificado.

### Verificación de existencias

Los productos sin disponibilidad quedan excluidos de las recomendaciones.

### Recomendación de alternativas

Cuando no existen coincidencias, el sistema puede ofrecer otros productos disponibles que cumplan los filtros aplicados.

### Integración con inteligencia artificial

Se utiliza GPT-5 Nano para generar respuestas comerciales a partir de la información proporcionada por el catálogo.

El prompt establece restricciones para evitar que el modelo invente productos, precios y existencias.

### Registro de consultas

El workflow incluye un nodo que permite registrar información sobre las consultas procesadas en Google Sheets.

## 🧩 Estructura del proyecto

```text
sli-shop/
│
├── README.md
│
└── alisonshop-workflow-publico.json
```

El archivo JSON contiene una versión pública del workflow desarrollado en n8n.

## 🛠️ Configuración

Para utilizar el proyecto:

1. Disponer de una instancia de n8n.
2. Importar el archivo `alisonshop-workflow-publico.json`.
3. Configurar las credenciales de Google Sheets.
4. Configurar el acceso a OpenAI.
5. Configurar las credenciales de WhatsApp Business.
6. Sustituir los identificadores de ejemplo por los correspondientes.
7. Configurar el webhook de Meta y comprobar las conexiones antes de activar el flujo.

**Importante:** el archivo público no incluye credenciales operativas. La configuración de la verificación del webhook de Meta debe realizarse por separado.

## Pruebas y validaciones

El workflow contiene validaciones para identificar consultas incompletas, clasificar resultados y preparar respuestas según la información disponible.

La incorporación de pruebas automatizadas independientes y casos de prueba documentados forma parte de las siguientes mejoras del proyecto.

## 📈 Mejoras previstas

* Ampliar la búsqueda para interpretar consultas combinadas de marca y modelo.
* Incorporar pruebas automatizadas de JavaScript.
* Mejorar el manejo de errores de servicios externos.
* Documentar resultados de pruebas funcionales.
* Reforzar la validación de los eventos recibidos mediante webhooks.
* Incorporar capturas y ejemplos de ejecución.

## Estado del proyecto

**Prototipo funcional en desarrollo.**

El workflow se desarrolló en un entorno de pruebas y se comparte como demostración técnica.

La versión pública requiere configuración adicional y no representa un servicio desplegado para uso productivo.

## Autor

**Enrique Crispín**

Estudiante de Ingeniería — Universidad Mayor de San Simón (UMSS).

Intereses profesionales: desarrollo de software, automatización de procesos, integración de APIs e inteligencia artificial aplicada.

**GitHub:** https://github.com/enrique-crispin26

**Repositorio:** https://github.com/enrique-crispin26/sli-shop
