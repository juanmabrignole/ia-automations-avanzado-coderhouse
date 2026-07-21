# Clase 2 · Prácticas en vivo (Lucas solo) · Instalación y guion

Dos prácticas, cinco workflows. Las armás antes de clase, las corrés en vivo mientras explicás.

---

## Práctica 1 · Un Reto, Tres Arquitecturas → versión Manager-Worker

**Archivos:** `p1_manager.json` · `p1_worker_a_investigador.json` · `p1_worker_b_redactor.json`

### Qué hace
Un Manager recibe un pedido de plan de lanzamiento, llama primero al Worker Investigador (que devuelve hallazgos de mercado) y después al Worker Redactor (pasándole esos hallazgos), y presenta el plan final. Es el patrón Manager-Worker de la teoría, funcionando.

### Instalación (10 minutos, antes de clase)
1. Importá los **3 archivos** como 3 workflows separados (Import from File).
2. Abrí `p1_manager.json`. En el nodo **consultar_investigador_de_mercado**, hacé clic en el selector de "Workflow" y elegí **"Clase 2 - Worker A - Investigador de Mercado"** de la lista (el JSON lo referencia por nombre, así que hay que re-seleccionarlo tras importar).
3. En el nodo **pedir_plan_de_lanzamiento**, mismo paso pero eligiendo **"Clase 2 - Worker B - Redactor de Plan"**.
4. Credencial de Groq en los 3 nodos "Chat Model (Groq)" de los 3 workflows (API key gratis en console.groq.com).
5. Activá los 2 Workers (Active = ON). El Manager podés dejarlo sin activar: se prueba desde el botón de chat del editor.

### Probar antes de clase
Abrí el chat del workflow Manager y escribí algo como:
> "Armame un plan de lanzamiento para una app de meditación, dirigida a profesionales con estrés laboral."

Deberías ver en el chat: el Manager llama primero al Investigador, después al Redactor con los hallazgos, y devuelve el plan. Mirá la pestaña **Executions** para ver las 3 ejecuciones (Manager + 2 sub-workflows).

### Guion en vivo (slides 14 a 16, ~20 minutos)
1. **Consigna** (slide 14): explicá el caso y las 3 versiones posibles. No hace falta construir la v1 (agente único) ni la v2 (agente con tools): son solo mencionadas para contraste.
2. **Tabla comparativa** (slide 15): completala hablando en voz alta, apoyándote en la matriz de costo/latencia/robustez vista antes del break.
3. **Demo en n8n** (slide 16): abrí el Manager, mostrá el canvas completo (Manager + los 2 Tool Workflow), explicá el contrato de datos (los campos `tema`, `publico_objetivo`, `hallazgos` en cada tool), y corré el ejemplo en vivo por el chat. Mientras corre, abrí la pestaña Executions y mostrá cómo el Manager dispara las 2 sub-ejecuciones.

### Si algo falla en vivo
- Si el Manager no llama a las tools en el orden correcto: es un tema de prompt, no de configuración. Podés simplemente decir "a veces el modelo se toma libertades en el orden" y volver a correr; es un buen gancho para hablar de la naturaleza probabilística de los agentes.
- Si un Worker tarda mucho: Groq es rápido, pero si la demo se cuelga, tené como plan B contar lo que "debería" pasar mostrando el canvas estático.

---

## Práctica 2 · Autopsia de una falla → construir, romper y reparar

**Archivos:** `p2_v1_rota.json` · `p2_v2_reparada.json`

### Qué hace
Un sistema Planner → Worker → Critic que procesa el reembolso de gastos de viaje de un empleado ficticio (Martín, 7 días, Córdoba). La versión rota aprueba un monto inflado porque el Critic solo revisa formato. La versión reparada recalcula y compara contra la política real.

### Instalación (5 minutos)
1. Importá **ambos archivos** como 2 workflows separados.
2. Credencial de Groq: en `p2_v1_rota.json` hay 1 nodo Chat Model; en `p2_v2_reparada.json` hay 2 (Worker y Critic). Asigná la credencial en los 3.
3. No hace falta activarlos: se ejecutan con el botón "Execute Workflow" (disparan desde el Manual Trigger).

### Probar antes de clase
1. Abrí `p2_v1_rota.json` y tocá "Execute Workflow". Resultado esperado: el Critic devuelve `resultado: "APROBADO"` con un monto de **$350** (7 días x $50 asumido). La política real de la empresa es $35/día ($245): el sistema aprobó de más y nadie lo notó.
2. Abrí `p2_v2_reparada.json` y tocá "Execute Workflow". Resultado esperado: el Worker calcula **$245** (7 x $35, porque ahora recibe la política), y el Critic recalcula y compara: debería dar `RESULTADO: APROBADO` porque coincide.
3. **Para que la reparación se note más en vivo**, podés forzar un error: en el nodo "Worker (usa la política real)" de la v2, cambiá momentáneamente el texto para pedirle que calcule mal (o simplemente corré un par de veces: los LLMs a veces cometen un error de cálculo menor) y mostrá cómo el Critic lo detecta y RECHAZA. Si siempre da bien a la primera prueba, no hace falta forzarlo: mostrar que audita y aprueba correctamente ya cumple el objetivo pedagógico.

### Guion en vivo (slides 17 a 19, ~20 minutos)
1. **Consigna** (slide 17): presentá el caso, conectándolo con la slide de Critique Loop de la teoría.
2. **La traza** (slide 18): mostrá los 3 pasos como "lo que va a pasar cuando lo corra" (no como enigma a resolver: se lo estás explicando a la clase de antemano).
3. **Demo en n8n** (slide 19):
   - Abrí `p2_v1_rota.json`, mostrá el canvas (Planner → Worker → Critic), ejecutalo en vivo y mostrá el resultado `APROBADO` con $350 en el panel de salida del nodo Critic.
   - Abrí el nodo Critic y mostrá el código: "solo mira si existen los campos, nunca el número". Ese es el momento aha.
   - Cambiá a `p2_v2_reparada.json`, mostrá el Planner con la política agregada, el Worker que ahora la usa, y el Critic que recalcula. Ejecutalo en vivo y mostrá el resultado correcto.
   - Cerrá conectando con la rúbrica: "esto es exactamente la gestión de fallos que vale 20% del Checkpoint 2".

### Nota pedagógica
El Critic de la v1 es un **nodo de código** (no un agente de IA) a propósito: muestra que el echo chamber no es un problema de "el modelo no es lo bastante inteligente", sino de **diseño del criterio de evaluación**. Un validador sin criterio propio es un echo chamber, sea código o sea IA. Vale la pena decirlo explícitamente en clase.

---

## Checklist general antes de la clase

- [ ] Cuenta de Groq activa con API key cargada como credencial en n8n
- [ ] Los 5 workflows importados y con las credenciales asignadas
- [ ] Los 2 Workers de la Práctica 1 con sus workflows re-seleccionados en los nodos Tool Workflow del Manager
- [ ] Los 2 Workers de la Práctica 1 activados (Active = ON)
- [ ] Ensayo completo de ambas prácticas al menos una vez, de punta a punta
- [ ] Pestaña Executions abierta y familiarizada, para mostrarla en vivo sin buscarla a los tropezones
