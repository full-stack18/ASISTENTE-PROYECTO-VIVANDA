# Tecnología · APK-TUTI 2.0, asistente inteligente de operaciones de tienda Vivanda

Redacta todos los documentos en español.

## Pila de referencia (decidida por la docente; no proponer alternativas sin justificación)
- **Lenguaje:** Python 3.11+.
- **Backend:** FastAPI (endpoint de conversación con el agente y endpoints de registro: marcas de tareas, mermas, incidencias).
- **Frontend:** página web simple (HTML + JavaScript) servida por el backend. Sin frameworks pesados.
- **Modelo de lenguaje:** Gemini API (Google AI Studio, nivel gratuito), consumida vía SDK en Python; clave en variable de entorno `GEMINI_API_KEY`, nunca en el código ni en el repositorio.
- **Agente con herramientas:** el modelo invoca herramientas (function calling) de dos tipos: herramientas de consulta, que llaman al servidor MCP, y herramientas de registro, que llaman al backend.
- **Recuperación (RAG):** fragmentación de los manuales de procedimientos operativos de la tienda (documentos de la organización) por sección; embeddings de Gemini; índice vectorial local (ChromaDB). Cada fragmento conserva metadatos: documento y sección.
- **Datos de la tienda:** SQLite con datos simulados: tareas y checklists, turnos y personal, productos con stock y precio, órdenes de proveedor, mermas e incidencias.
- **Acceso a datos:** servidor MCP propio en Python (FastMCP), **solo lectura**, construido en la semana 4 (`mcp_server/`). Las consultas del backend a la base se hacen a través de las herramientas del servidor MCP. Las escrituras (marcas de tareas, mermas, incidencias) las realiza el backend, nunca el servidor MCP.
- **Control de versiones:** Git y GitHub.

## Restricciones técnicas
- Toda respuesta del modelo sobre un procedimiento debe incluir la cita recuperada (documento y sección); si la recuperación no devuelve fragmentos pertinentes, el sistema responde que no encontró la información.
- Fecha-hora de toda marca de tarea, merma e incidencia asignada por el servidor, no por el cliente.
- Salidas estructuradas (JSON validado con Pydantic) para el reporte de cierre de turno y la verificación de recepción contra la orden del proveedor.
- Registro de trazas por consulta (pregunta, herramientas invocadas, fragmentos recuperados, respuesta, tokens) en archivo local.
- Acceso por rol: cada usuario solo consulta lo que su rol permite; no se exponen DNI, teléfonos ni correos del personal (Ley N.° 29733).
- Costo cero en desarrollo: solo servicios con nivel gratuito.
- Soluciones simples y legibles, de nivel universitario; sin sobreingeniería.