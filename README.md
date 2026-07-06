# Commerce Hub — Brief Triage & Fee Compliance Agent

**Entrega Final — AI Automation, Coderhouse**
Alumna: Cristina Mourin

Ecosistema de automatización IA autónomo que resuelve el intake, clasificación, validación de scope y ruteo de briefs creativos de una agencia LATAM, de extremo a extremo.

---

## ¿Qué hace?

Cada brief que ingresa se clasifica mediante IA según dos criterios en una sola llamada:
- **Stopper / Non-Stopper**: si faltan assets críticos que bloquean la producción.
- **Dentro / fuera del fee**: si el trabajo está incluido en el scope contratado por el cliente.

Según el resultado, el brief se **auto-aprueba y rutea**, o se deriva a **validación humana (HITL)** antes de continuar.

---

## Stack

| Componente | Herramienta |
|---|---|
| Orquestador | n8n Cloud |
| Base de datos | Google Sheets (4 hojas vinculadas) |
| Motor de IA | OpenAI GPT-4o-mini |
| Canal de salida | Gmail |
| Human-in-the-Loop | Nodo Wait (modo Webhook) |

---

## Flujo end-to-end

1. **Trigger** — nueva fila en la hoja `briefs`.
2. **Lookup de cliente** — recupera fee y scope de la hoja `clientes`.
3. **Clasificación IA** — clasifica Stopper/fee y responde en JSON.
4. **Registro** — cada decisión se guarda en `ai_logs` (trazabilidad).
5. **Switch** — rutea a Auto-aprobado o HITL.
6. **Auto-aprobado** — actualiza estado y notifica al squad.
7. **HITL** — envía mail con link de aprobación y pausa el flujo hasta la respuesta humana.
8. **Cierre** — al aprobar, estado pasa a "Aprobado por Humano".

---

## Manejo de errores

Retry on Fail (3 intentos) + On Error "Continue using error output" en los nodos críticos, con ramas de error dedicadas.

---

## Enlaces y evidencias

- **Base de datos (modo lectura):** https://docs.google.com/spreadsheets/d/1BylaQILkq8IqkCnOu5Z3tOAHg39j_OzfF9OP1d0dP6Q/edit?usp=sharing
- **Video demo:** https://drive.google.com/file/d/1ZnRUKucZRc4vojwDz4la-1nX9ieTvJNF/view?usp=sharing
- **Diagrama de arquitectura:** ver `Entrega_Final_Commerce_Hub.pdf`
- **Lógica del flujo:** archivo `.json` en este repositorio
- **Evidencias:** screenshots `01` a `07` en este repositorio

---

## Nota técnica

Se utilizó Google Sheets como base de datos relacional (hojas vinculadas por `brief_id` y `cliente_id`) en lugar de Airtable/Notion, por continuidad con el ecosistema construido a lo largo del curso. El modelo relacional exigido se cumple: relaciones por clave, campos de estado y trazabilidad completa.
