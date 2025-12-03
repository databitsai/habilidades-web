# Instrucciones para configurar n8n

## Problema 1: Error de CORS

Para resolver el error de CORS en n8n, necesitas configurar los headers CORS en el nodo "Respond to Webhook":

### Pasos:

1. **Abre tu workflow en n8n**
2. **Selecciona el nodo "Respond to Webhook"**
3. **Ve a la pestaña "Settings" (Configuración)**
4. **En "Response Headers", agrega estos headers:**

```
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: POST, OPTIONS
Access-Control-Allow-Headers: Content-Type
```

O si quieres ser más específico y solo permitir tu dominio:

```
Access-Control-Allow-Origin: https://tu-dominio.com
Access-Control-Allow-Methods: POST, OPTIONS
Access-Control-Allow-Headers: Content-Type
```

**Nota:** Si usas un dominio específico, reemplaza `https://tu-dominio.com` con la URL donde está alojada tu página HTML (ej: `file://` para archivos locales, o la URL completa si está en un servidor).

---

## Problema 2: La respuesta está vacía y no incluye `thread_id`

**⚠️ PROBLEMA ENCONTRADO:** 

Tu "Response Body" está configurado así:
```json
{{ { "reply": $json.message.content } }}
```

Pero en el INPUT de tu nodo "Respond to Webhook" veo que los datos tienen:
- `"output"`: el mensaje del bot
- `"threadId"`: el ID del thread (ej: "thread_OZmCpuU3yb0jXw1DGkrvswFi")

**El problema:** `$json.message.content` NO EXISTE en tus datos, por eso la respuesta está vacía.

### ✅ SOLUCIÓN CORRECTA:

Debes cambiar el "Response Body" para que use los campos correctos:

### Pasos:

1. **Selecciona el nodo "Respond to Webhook"**
2. **Ve a la pestaña "Parameters"**
3. **En "Response Body", reemplaza completamente la expresión actual con:**

```
{{ { "reply": $json.output, "thread_id": $json.threadId } }}
```

**Explicación:**
- `$json.output` → Toma el mensaje del bot que está en el campo "output" del INPUT
- `$json.threadId` → Toma el thread ID que está en el campo "threadId" del INPUT

### 🔍 Verificación antes de guardar:

Después de cambiar la expresión, deberías poder hacer "Test step" o ver una vista previa que muestre:
```json
{
  "reply": "¡Hola! Soy Althea...",
  "thread_id": "thread_OZmCpuU3yb0jXw1DGkrvswFi"
}
```

Si no ves la vista previa correctamente, revisa que los nombres de los campos en el INPUT coincidan exactamente (mayúsculas/minúsculas importan).

---

## Verificación

Después de hacer estos cambios:

1. **Activa tu workflow en n8n**
2. **Prueba desde tu página HTML**
3. **Abre la consola del navegador (F12)**
4. **Deberías ver:**
   - No más errores de CORS
   - Un mensaje en consola: "Thread ID guardado: thread_xxxxx"
   - Las respuestas del bot funcionando correctamente

