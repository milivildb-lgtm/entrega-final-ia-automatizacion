# Entrega Final — Ecosistema de Automatización IA Autónomo

**Alumna:** Milagros Vilches · **Curso:** IA Automatización — Coder

**Caso de uso:** Pipeline de contenido "Aranceles" para una ALyC (mercado de capitales). A partir de una idea semilla cargada en Airtable, GPT-4o (OpenAI) redacta un post de LinkedIn usando una base de conocimiento validada (RAG), un humano lo aprueba y recién ahí se publica.

| Categoría | Herramienta |
|---|---|
| Orquestador | n8n (2 workflows) |
| Base de datos | Airtable (tablas "Piezas" y "Base de Conocimiento") |
| Procesamiento IA | OpenAI GPT-4o con contexto RAG (vía HTTP Request, `max_tokens: 400`) |
| Canal de salida | Slack |

## Dónde está cada criterio de la rúbrica

| Criterio (20% c/u) | Dónde encontrarlo |
|---|---|
| 1. Mapa de arquitectura | `docs/Documentacion Entrega Final.pdf` → sección 1 · `docs/diagrama de arquitectura.png` |
| 2. Estructuras de datos | PDF → sección 2 (tablas de Airtable + esquemas JSON de cada integración) |
| 3. Optimización de costos | PDF → sección 3 (matriz de decisión de modelos por tarea + ahorro estimado) |
| 4. Seguridad y resiliencia | PDF → sección 4 (minimización de datos, Error Handlers, anti-loop, puntos HITL) |
| 5. Dashboard de control | Link público: https://airtable.com/appcQgDfOTgjAAShp/shrsHOxgQbrL1XaVB |

## Archivos técnicos

- `workflows/generar borrador.json` — Workflow 1 (n8n): Trigger → filtro anti-loop → RAG → OpenAI GPT-4o → Airtable → Slack (solicitud de aprobación). Incluye rama de error.
- `workflows/publicar tras aprobacion.json` — Workflow 2 (n8n): Trigger → filtro HITL → Slack (publicación) → Airtable. Incluye rama de error.
- Base de datos en modo lectura (dos vistas públicas):
  - Tabla "Piezas" (centro de comando), agrupada por Estado: https://airtable.com/appcQgDfOTgjAAShp/shrsHOxgQbrL1XaVB
  - Tabla "Base de Conocimiento" (fuente RAG): https://airtable.com/appcQgDfOTgjAAShp/shrIidx9dYyfyZU19
- `evidencias/` — capturas de ejecuciones (camino feliz, camino infeliz, pausa HITL, Slack, dashboard).

## Check de seguridad

- **Filtro anti-bucle:** sí — el Workflow 1 solo procesa filas con Estado = "Generando".
- **Tipos de datos correctos en filtros:** sí — "Aprobado" se compara como booleano, "Estado" como texto, "Aranceles" viaja como número.
- **Prompt dinámico:** sí — Comitente, Tipo de Operación, Aranceles, Idea Semilla y el contexto RAG se inyectan en cada ejecución.
- **Credenciales:** ninguna API key está en los JSON; viven como credenciales cifradas en n8n.
