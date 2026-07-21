# Demo Build-a-Minimal-Agent · Workflows pre-armados (backup)

Tres versiones del agente de soporte de "TiendaFacil" (empresa ficticia). El plan A es construir en vivo; estos JSON son el plan B y también sirven para ensayar. Los tres usan la misma base: Chat Trigger → AI Agent (Tools Agent) → GPT-4o + Memoria + herramienta de Google Sheets.

## Archivos
| Archivo | Qué es | Cuándo usarlo en clase |
|---|---|---|
| `demo_v1_rota_descripcion.json` | Tool llamada "tool1" con descripción "Busca cosas." | Rotura 1 (20:15). Si la construcción en vivo se traba, importás esto y mostrás el fallo directamente |
| `demo_v2_rota_bucle.json` | Prompt que exige verificación infinita + Max Iterations en 50 | Rotura 2 / Clínica de Fallos (20:20). Este es el "JSON roto del cliente" que se importa EN VIVO como parte del guion |
| `demo_v3_final.json` | Versión que cumple la rúbrica del checkpoint: prompt modular, tools con descripción, Max Iterations 8, log de auditoría | Cierre de la demo y referencia para los alumnos (se sube al repositorio después de clase) |

## Setup (una vez, antes de ensayar)

**1. Base de conocimiento en Google Sheets**
Creá una planilla "FAQ TiendaFacil" con 2 columnas: `pregunta` y `respuesta`. Cargá 5-8 filas, por ejemplo:
- ¿Cuánto tarda el envío? / Entre 3 y 5 días hábiles a todo el país.
- ¿Cómo hago una devolución? / Tenés 30 días desde la entrega; se gestiona desde Mi Cuenta > Pedidos.
- ¿Los productos tienen garantía? / Sí, 12 meses de garantía oficial de fábrica.
- ¿Puedo pagar en cuotas? / Sí, hasta 12 cuotas con tarjetas bancarias.

**2. Importar y conectar (en cada workflow)**
1. Workflows → Import from File.
2. Nodo del modelo: seleccioná tu credencial de OpenAI (o reemplazá el nodo por Anthropic Chat Model si preferís Claude).
3. Nodo de Google Sheets (tool): credencial + Document + Sheet de la planilla FAQ.
4. Solo en v3: credencial de Gmail en "escalar_a_humano" y "Log de auditoría", y tu email real en el campo sendTo de ambos.

**3. Probar**
Abrí el chat del Chat Trigger (botón Chat) y preguntá "¿cuánto tarda el envío?".

## Guion de cada rotura

**v1 (instrucción huérfana):** preguntá algo que SÍ está en la base. El agente suele responder sin usar la tool o inventando, porque "tool1: Busca cosas." no le dice cuándo usarla. Abrí el log de ejecución, mostrá el Thought, y reparalo en vivo: renombrá la tool a `buscar_base_conocimiento` y pegá la descripción buena (está en la v3). Volvé a preguntar: ahora la usa.

**v2 (bucle):** preguntá algo que NO está en la base (ej. "¿venden autos?"). El prompt le exige "verificar con 3 búsquedas distintas y reintentar hasta estar 100% seguro", así que encadena búsquedas sin parar; con Max Iterations en 50 el log se llena de vueltas. Frase de Lucas: "cada vuelta son tokens facturados". Reparación: Max Iterations a 5-10 en las opciones del agente + borrar la regla de verificación infinita del prompt.

**v3 (final):** mostrá el System Prompt modular (Rol → Ámbito → Objetivo → Reglas), las 2 tools con sus descripciones, el guardrail en 8 y el email de "Tarea completada" que llega al cerrar cada interacción. Conectalo explícitamente con la rúbrica: observabilidad 35% + arquitectura de tools 30% + guardrails 35%.

## Notas técnicas
- Los JSON usan versiones estables de los nodos: si n8n te ofrece "actualizar nodo a la última versión", podés aceptar sin drama.
- Cada workflow trae una sticky note amarilla con el bug plantado y su reparación: NO compartir pantalla con la sticky visible antes del reveal (o borrala para la clase).
- Los nodos con credenciales van a mostrar un warning rojo hasta que asignes las tuyas: es lo esperado tras importar.
- Costo de ensayo: con GPT-4o y estas pruebas cortas, centavos. El bucle de v2 con Max Iterations 50 puede consumir más: cortalo con Stop después de mostrar 6-8 vueltas del log.
