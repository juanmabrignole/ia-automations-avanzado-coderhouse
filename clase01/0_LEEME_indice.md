# Clase 01 — Índice de ejercicios

## Material de Lucas Barrantes (tal cual, comisión 97480) — usar como referencia principal
- `demo_v1_rota_descripcion.json` · `demo_v2_rota_bucle.json` · `demo_v3_final.json` — las 3 versiones del agente de soporte de "TiendaFacil" para la demo Build-a-Minimal-Agent / Clínica de Fallos.
- `desafio_agente_hierro_solucion.json` — solución de referencia del Desafío del Agente de Hierro (aprobación de facturas).
- `README_demo_agente.md` y `README_desafio_hierro.md` — guiones de clase de Lucas: setup, timing y qué decir en cada rotura. Leer estos antes que nada.
- `FAQ TiendaFacil.xlsx` — la base de conocimiento (Google Sheets) que usan los tres `demo_v*.json`. Subila a Google Sheets y conectala en el nodo `Tool` de cada workflow.

## Agregados propios (no son de Lucas, complementan su material)
- `1_escenarios_flujo_o_agente.json` — los 6 escenarios de la actividad "¿Flujo o agente?" (tomados de las slides de Lucas) con respuesta y justificación para el instructor.
- `2_apertura_workflow1_captura.json` y `3_apertura_workflow2_dashboard.json` — la apertura "Queremos conocerte" (formulario + dashboard en vivo) que Lucas usa en su clase pero no estaba en este ZIP como JSON exportable; los reconstruí a partir de su slide 6.

## Notas
- Los `demo_v*.json` y el desafío usan **OpenAI (GPT-4o)** como Chat Model, no Groq — respetá eso al importar y usá tu propia credencial de OpenAI (o cambiá el nodo por Anthropic si preferís Claude).
- `demo_v3_final.json` y el desafío usan **Gmail** (log de auditoría y aprobación humana vía Send and Wait) — necesitás credencial de Gmail y tu email real en los campos `sendTo`.
