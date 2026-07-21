# Clase 01 — Ejercicios: guía rápida para el instructor

Importá cada `.json` en tu instancia de n8n (Workflows → Import from File) y probalo antes de la clase. Los agentes usan **Groq** (`lmChatGroq`) como Chat Model — reemplazá `REEMPLAZAR_CON_TU_CREDENCIAL` por tu credencial real. Los workflows de apertura usan **Google Sheets** — reemplazá `REEMPLAZAR_CON_TU_SPREADSHEET_ID`.

Esta versión incorpora material de Lucas Barrantes (comisión 97480): la apertura "Queremos conocerte", los 6 escenarios de la actividad de decisión, y el antipatrón "instrucción huérfana".

## 1. `1_escenarios_flujo_o_agente.json` — Test de decisión (15 min)
Data (no workflow) con los 6 escenarios de Lucas + respuesta y justificación, para la votación en vivo.

## 2-3. Apertura: "Queremos conocerte" (5 min, antes de arrancar)
- `2_apertura_workflow1_captura.json`: Form Trigger → Google Sheets (Append). El formulario de icebreaker.
- `3_apertura_workflow2_dashboard.json`: Webhook GET → Google Sheets (Read) → Code (arma HTML) → Respond to Webhook. Dashboard en vivo que se refresca cada 10s, para proyectar mientras responden el formulario.

Antes de la clase: creá el Google Form... no, es un **n8n Form Trigger** (no Google Forms) — el link te lo da el nodo Form Trigger de n8n. Cargá los campos de icebreaker que prefieras (ejemplo incluido: país, edad, rol, industria, experiencia con n8n, interés, mate o café) y una Google Sheet con las mismas columnas.

## 4. `4_build_minimal_agent_REFERENCIA.json` — Build-a-Minimal-Agent (30 min)
Versión funcional de referencia (backup). En vivo lo construís paso a paso con los alumnos: Chat Trigger → AI Agent (Tools Agent) → Groq Chat Model + Memory + 1 Tool. Reemplazá la URL del `Consultar_Base_Conocimiento` por un endpoint real.

## 5-8. Clínica de fallos — usar como comodín, sin duración fija
Cada uno tiene una sticky note con la pregunta pero SIN la respuesta, para que los alumnos diagnostiquen en vivo. Respuestas para vos:

- **`5_clinica_fallos_1_bucle_infinito.json`**: `maxIterations` en 50 (muy por encima de la buena práctica de 5-10) y el system prompt pide seguir buscando "hasta estar 100% seguro", sin condición de salida clara. Fix: bajar `maxIterations` a 5-10 y agregar una instrucción explícita de cuándo debe responder.

- **`6_clinica_fallos_2_falta_memoria.json`**: no tiene ningún nodo de memoria conectado (`ai_memory`). Con dos mensajes seguidos, el agente no recuerda el primero. Fix: agregar `Window Buffer Memory`.

- **`7_clinica_fallos_3_RAG_vs_agente.json`**: se llama "Agente de Atención al Cliente" y promete "decidir y ejecutar acciones", pero el nodo real es un `chainLlm` (Basic LLM Chain) sin ninguna Tool conectada — no puede ejecutar nada. Fix: migrar a un nodo `agent` con las Tools correspondientes, o dejar de llamarlo "agente" si solo responde preguntas.

- **`8_clinica_fallos_4_instruccion_huerfana.json`** (nuevo, de Lucas): la tool se llama "Tool_1" y su descripción dice literalmente "Busca cosas." El modelo no tiene forma de saber cuándo usarla ni con qué datos — la ignora, la usa mal, o alucina el input. Fix: la descripción de una tool ES su prompt — escribila como para un empleado nuevo: cuándo usarla, con qué datos exactos, y qué NO hacer. Ejemplo de buena descripción: "Busca un producto en el catálogo por nombre o SKU. Usar solo cuando el cliente pregunte por disponibilidad o precio de un producto específico."

## 9. `9_desafio_agente_de_hierro_SCAFFOLD.json` — Desafío en equipo (15 min diseño + 8 min puesta en común)
Caso: factura con monto que no coincide con la orden de compra. Ajustado a la consigna de Lucas: el diseño se hace primero en **Miro o papel, no en n8n**, con 4 requisitos obligatorios (herramienta de lectura de facturas, nodo de validación lógica, punto de aprobación humana, condición de parada por intentos). El JSON queda como scaffold opcional para prototipar en n8n después, si el tiempo alcanza.

## Qué NO se encontró
En la carpeta de Drive de la comisión 97480 solo había la presentación de Lucas (27 slides) — no había archivos JSON de n8n propios de él. Si los tiene en otro lado, pasámelos y los sumo.
