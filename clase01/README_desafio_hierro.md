# Desafío "Agente de Hierro" · Solución de referencia

`desafio_agente_hierro_solucion.json` es la implementación en n8n del caso **"Aprobación de Facturas con Discrepancias"**. Los alumnos diseñan la arquitectura en papel/Miro (así lo pide el programa); este workflow es para que ustedes muestren una implementación posible DESPUÉS de la puesta en común, o lo suban al repositorio como material de estudio.

## La arquitectura (y por qué)

```
Form (cargar factura)
   → Calcular discrepancia (Set, lineal)
   → IF ¿discrepancia <= 2%?
        SÍ → Aprobada automática            ← lo determinista, sin IA
        NO → Agente "Analista" (Max Iter 5) ← solo lo ambiguo
              → Gmail Send and Wait          ← aprobación humana
              → IF ¿aprobó?
                   SÍ → Aprobada por humano
                   NO → Rechazada por humano
```

Cumple los 4 requisitos de la consigna y encarna la regla de oro de la clase:
1. **Herramienta de lectura** → el Form Trigger captura la factura (en producción sería un trigger de email o Drive; el form simplifica la demo).
2. **Validación lógica** → cálculo de discrepancia + IF con tolerancia del 2%: es una regla clara, así que es un nodo IF, no un agente.
3. **Aprobación humana** → Gmail "Send and Wait": el flujo queda pausado hasta que el gerente toca Aprobar/Rechazar en el email. El agente solo RECOMIENDA; nunca aprueba.
4. **Condición de parada por intentos** → Max Iterations = 5 en el agente.

Detalle fino para comentar en clase: el prompt del analista tiene una regla anti-alucinación ("si el motivo es 'Sin detalle' y la discrepancia supera el 10%, nunca recomiendes APROBAR").

## Setup
1. Import from File → `desafio_agente_hierro_solucion.json`.
2. Credencial de OpenAI en el Chat Model (o cambiarlo por Anthropic).
3. Credencial de Gmail en "Aprobación humana (email)" y tu email real en sendTo (a ese buzón llega el botón de aprobar/rechazar).
4. Activar el workflow y abrir la Production URL del form.

## Guion de demo (3 minutos)
1. Cargar una factura SIN discrepancia (factura $100.000, OC $100.000) → sale por la rama automática, sin gastar un token. Ese es el punto: lo determinista no paga peaje de IA.
2. Cargar una con discrepancia (factura $118.000, OC $100.000, motivo "ajuste por flete") → el agente analiza, llega el email, aprobás desde el mail y el flujo termina en "Aprobada por humano".
3. Mostrar en el canvas la frontera: hasta el IF todo es riel de tren; el agente toca solo la parte que necesita criterio.

## Notas técnicas
- El nodo "Aprobación humana" usa la operación **Send and Wait** de Gmail. Si al importar en tu versión el nodo muestra parámetros incompletos, abrilo y verificá que Operation = "Send and Wait for Response" y Response Type = "Approval"; según la versión puede pedir re-seleccionar esos dos campos.
- Mientras espera la aprobación, la ejecución queda en estado "Waiting" en la pestaña Executions: buen momento para mostrar observabilidad.
- Las sticky notes del canvas explican cada decisión de diseño; sirven tal cual para compartir pantalla.
