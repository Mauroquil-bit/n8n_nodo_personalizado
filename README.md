# Prompt para construir un nodo personalizado en N8N con IA

Gracias por tu interés. Acá está el prompt exacto (adaptado y genérico) que usé para construir mi nodo desde cero, junto con los pasos para que vos puedas hacer lo mismo.

No necesitás saber TypeScript de antemano. La IA genera el código. Tu trabajo es entender qué querés conectar y cómo responde tu API.

---

## Requisitos previos

### 1. Instalar Node.js

Descargá la versión **LTS** (18.x o 20.x) desde:
👉 https://nodejs.org

Verificá la instalación abriendo una terminal:

```bash
node -v
npm -v
```

### 2. Instalar N8N localmente

```bash
npm install -g n8n
n8n start
```

Abrí el navegador en `http://localhost:5678` y creá tu cuenta local.

> **Si usás PC corporativa con certificados SSL propios**, arrancá N8N así:
> ```bash
> NODE_TLS_REJECT_UNAUTHORIZED=0 n8n start
> ```

---

## Estructura de carpetas que vas a tener al final

```
n8n-nodes-[tu-nodo]/
├── credentials/
│   └── [TuNodo]Credentials.ts
├── nodes/
│   └── [TuNodo]/
│       ├── [TuNodo].node.ts
│       └── icon.svg
├── index.ts
├── package.json
└── tsconfig.json
```

---

## Cómo completar el prompt

Antes de pegar el prompt en la IA, reemplazá cada placeholder con tus datos:

| Placeholder | Descripción | Ejemplo |
|---|---|---|
| `[NOMBRE_PAQUETE]` | Nombre npm del nodo | `n8n-nodes-consulta-facturas` |
| `[NOMBRE_CLASE]` | Nombre PascalCase del nodo | `ConsultaFacturas` |
| `[DESCRIPCION_NODO]` | Qué hace el nodo en una oración | `Consulta el estado de una factura en el sistema de facturación` |
| `[URL_BASE_API]` | URL base de tu API | `https://api.miempresa.com` |
| `[RUTA_ENDPOINT]` | Path del endpoint específico | `/v1/facturas/consultar` |
| `[METODO_HTTP]` | Método HTTP | `POST` |
| `[NOMBRE_HEADER_AUTH]` | Header de autenticación | `apikey` o `Authorization` |
| `[PARAMETROS_ENTRADA]` | Lista de campos que ingresa el usuario | ver ejemplo abajo |
| `[ESTRUCTURA_RESPUESTA_JSON]` | Ejemplo real de respuesta de tu API | ver ejemplo abajo |

### Ejemplo de `[PARAMETROS_ENTRADA]`:
```
- numero_factura: texto, requerido, "Número de la factura a consultar"
- ambiente: opciones (PROD / DEV), default PROD
- api_key: texto enmascarado (password), requerido
```

### Ejemplo de `[ESTRUCTURA_RESPUESTA_JSON]`:
```json
{
  "status": "OK",
  "data": {
    "factura": {
      "numero": "F-001234",
      "estado": "PAGADA",
      "importe": 15000.00,
      "fecha_vencimiento": "2025-03-15"
    }
  }
}
```

---

## EL PROMPT

Copiá todo lo que está entre las líneas `---INICIO---` y `---FIN---` y pegalo en tu IA:

---INICIO---

Sos un experto en desarrollo de nodos personalizados para N8N (community nodes).
Necesito que me ayudes a crear un nodo N8N desde cero con los siguientes datos.

## Datos del nodo

- Nombre del paquete npm: `[NOMBRE_PAQUETE]`
- Nombre de la clase principal: `[NOMBRE_CLASE]`
- Descripción: [DESCRIPCION_NODO]

## API a integrar

- URL base: `[URL_BASE_API]`
- Endpoint: `[RUTA_ENDPOINT]`
- Método HTTP: `[METODO_HTTP]`
- Autenticación: header `[NOMBRE_HEADER_AUTH]` con un API Key como valor

## Parámetros que el usuario ingresa en N8N

[PARAMETROS_ENTRADA]

## Estructura de la respuesta JSON de la API

```json
[ESTRUCTURA_RESPUESTA_JSON]
```

## Requisitos técnicos obligatorios

1. TypeScript estricto con interfaces para los tipos de request y response
2. Sin dependencias externas — usar únicamente `this.helpers.httpRequest` de N8N
3. Manejo de errores con `NodeOperationError` (importado de `n8n-workflow`)
4. Soporte para `continueOnFail` — si falla un item, que el flujo pueda seguir
5. Procesar múltiples items en loop (batch processing con `this.getInputData()`)
6. El API Key debe ser un parámetro del nodo con `typeOptions: { password: true }` — NO usar el sistema de Credentials de N8N (tiene un bug conocido en versiones 2.x que genera pantalla blanca)
7. Incluir un archivo SVG de icono genérico (cuadrado 60x60)

## Archivos que necesito que generes

1. `package.json` completo con scripts `build` que incluya `tsc` y copia del SVG con `copyfiles`
2. `tsconfig.json`
3. `index.ts` con exports del nodo y la credential
4. `credentials/[NOMBRE_CLASE]Credentials.ts`
5. `nodes/[NOMBRE_CLASE]/[NOMBRE_CLASE].node.ts` con toda la lógica
6. `nodes/[NOMBRE_CLASE]/icon.svg` (SVG genérico)
7. Comandos para hacer el build e instalar el nodo en N8N local

Por favor generá todos los archivos con el código completo, explicá brevemente las secciones clave del nodo principal, y al final decime cómo verificar que el nodo aparece y funciona en N8N.

---FIN---

---

## Con qué IAs podés usar este prompt

| IA | Acceso | Calidad | Cómo usarla |
|---|---|---|---|
| **ChatGPT (GPT-4o)** | Gratis con límite diario | Muy buena | chat.openai.com |
| **Google Gemini** | Gratis | Buena, contexto largo | gemini.google.com |
| **Copilot (Microsoft)** | Gratis en Windows/Edge | Buena | copilot.microsoft.com |
| **Claude.ai** | Pago (plan Pro) | Excelente para código | claude.ai |

### Cómo iterar cuando algo no funciona

El proceso no es "pegás el prompt y funciona todo". Es una conversación:

1. Pegás el prompt → la IA genera los archivos
2. Copiás los archivos, ejecutás `npm run build`
3. Si hay errores de TypeScript → copiás el error completo y se lo mandás a la misma IA: _"Tuve este error al compilar, ayudame a corregirlo: [pegar error]"_
4. Si el nodo no aparece en N8N → le pedís que revise el `package.json` (el campo `n8n.nodes` debe estar)
5. Si la API responde con un formato diferente al que esperabas → pegás la respuesta real y pedís que adapte el código

Cada corrección es aprendizaje. Después de 3 o 4 iteraciones ya entendés la estructura y podés modificar solo.

---

## Build e instalación del nodo

Una vez que tenés los archivos generados por la IA:

```bash
# Dentro de la carpeta del nodo
npm install
npm run build

# Instalar en N8N local
cd ~/.n8n/custom/
npm install /ruta/completa/a/tu-nodo/

# Reiniciar N8N
n8n start
```

Abrí `http://localhost:5678`, creá un flujo nuevo y buscá el nombre de tu nodo en el panel de nodos. Si aparece, está instalado correctamente.

---

## Problema frecuente: pantalla blanca al usar el nodo

**Síntoma:** abrís el nodo en el canvas y la pantalla se pone blanca o el panel no carga.

**Causa:** un bug en N8N 2.x con el sistema de Credentials cuando el nodo intenta acceder a ellas.

**Solución:** no usar Credentials de N8N. En cambio, agregar el API Key directamente como parámetro del nodo con `typeOptions: { password: true }` — el valor queda enmascarado en la UI pero se puede ingresar sin problema. El prompt ya incluye esta instrucción.

---

Cualquier duda, respondé este mensaje. Suerte con tu nodo.

