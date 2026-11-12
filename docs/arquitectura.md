# Arquitectura del sistema AlisonShop

## 1. Descripción general

AlisonShop es un sistema de automatización comercial desarrollado mediante n8n. Integra WhatsApp Business, Google Sheets y OpenAI para gestionar consultas de clientes sobre celulares.

## 2. Arquitectura del flujo

El sistema utiliza la siguiente secuencia:

WhatsApp Business → Webhook POST → Interpretación del mensaje → Validación → Google Sheets → JavaScript → Clasificación → OpenAI → Respuesta por WhatsApp.

## 3. Componentes principales

### 3.1. Recepción de mensajes
El nodo Meta mensajes POST recibe las notificaciones enviadas mediante el webhook de Meta.

### 3.2. Interpretación del mensaje
El nodo Interpretar mensaje Meta utiliza JavaScript para extraer el nombre del cliente, número telefónico, producto solicitado y presupuesto.

### 3.3. Validación de datos
El nodo SolicitudCliente organiza los datos recibidos. Posteriormente, el nodo If comprueba que el cliente y el producto no estén vacíos.

### 3.4. Consulta del inventario
El nodo Get row(s) in sheet consulta los productos almacenados en Google Sheets.

### 3.5. Procesamiento con JavaScript
El nodo Code in JavaScript normaliza los registros del inventario y procesa las búsquedas.

Sus responsabilidades incluyen:
- Normalización de marcas y modelos.
- Conversión de precios y existencias.
- Filtrado según presupuesto.
- Exclusión de productos sin stock.
- Identificación de coincidencias.
- Generación de alternativas.
- Limitación de resultados.

### 3.6. Clasificación de resultados
El nodo Clasificar resultado utiliza un Switch para identificar tres situaciones:

- Encontrado.
- Solo alternativas.
- Sin resultados.

### 3.7. Generación de respuestas
El nodo Message a model utiliza GPT-5 Nano para redactar respuestas comerciales basadas en los datos del inventario.

Cuando no existen resultados, el sistema prepara una respuesta alternativa sin necesidad de consultar al modelo.

### 3.8. Registro y envío
Los nodos Preparar respuesta final y Preparar sin resultados organizan la información.

Finalmente:
- Guardar consulta registra los datos en Google Sheets.
- Send message envía la respuesta al cliente mediante WhatsApp Business.

## 4. Manejo de consultas inválidas

Cuando faltan datos obligatorios, el nodo If dirige la ejecución hacia error de validación, que prepara una respuesta solicitando información adicional.

## 5. Tecnologías utilizadas

- n8n.
- JavaScript.
- WhatsApp Business API.
- Google Sheets.
- OpenAI GPT-5 Nano.
- Webhooks HTTP.
- JSON.

## 6. Alcance

La arquitectura documenta el workflow público de AlisonShop. Las conexiones externas requieren credenciales y configuración propias para su ejecución.

El proyecto se presenta como un prototipo técnico y no como un servicio actualmente desplegado en producción.
