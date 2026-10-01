# Producto · Asistente inteligente de operaciones de tienda Vivanda

Redacta todos los documentos (requisitos, diseño, tareas) y todas las respuestas en español.

## Propósito
Actuar como supervisor virtual de una tienda Vivanda: guiar al personal en la apertura, recepción de mercadería, limpieza, etiquetado y cierre, y centralizar checklists, mermas e incidencias para que las tareas incumplidas y las pérdidas se detecten a tiempo y no recién en el conteo del fin de semana.

## Usuarios
- **Supervisor de turno** (principal): valida el checklist de su turno con hora real de cada tarea, ve quién está asignado, recibe alertas de tareas pendientes y registra incidencias.
- **Jefe de tienda**: consulta el reporte de cierre de turno (cumplimiento, incidencias y mermas) y el estado general de la operación.
- **Reponedores**: registran productos en mal estado o mermas en el momento y consultan inventario disponible y precios.
- **Encargado de recepción y almacén**: verifica lo recibido contra la orden del proveedor y registra diferencias.

## Capacidades del producto
1. Validación del checklist de tareas de apertura y cierre de tienda, guardando usuario y fecha-hora automática de cada tarea, mediante el servidor MCP (solo lectura) para consultar el checklist y el backend para registrar la marca.
2. Registro de productos en mal estado o mermas, con producto, cantidad y motivo.
3. Registro de incidencias, con tipo, descripción, responsable y fecha-hora del servidor.
4. Verificación de la recepción de mercadería contra la orden del proveedor, señalando cada ítem con diferencia (MCP).
5. Alerta de tareas pendientes antes del cierre del turno, con su responsable (MCP).
6. Consulta de inventario disponible y precios vigentes (MCP).
7. Consulta de horarios y turnos del personal, solo lectura (MCP).
8. Consulta de manuales de procedimientos operativos dentro de tienda mediante RAG, con cita del documento fuente.
9. Reporte de cierre de turno con porcentaje de cumplimiento, incidencias y resumen de mermas por motivo (MCP).

## Reglas no negociables
- **Fuentes:** responde sobre procedimientos solo con el contenido de los manuales de procedimientos operativos de la tienda que la organización tiene cargados. Si el procedimiento no está allí, lo declara y deriva al supervisor de turno o al jefe de tienda.
- **Cita:** toda respuesta sobre un procedimiento incluye el documento y la sección de donde proviene.
- **Discrepancias:** cuando dos documentos del corpus difieren, muestra ambos y no decide por el usuario.
- **Hora automática:** la fecha-hora de toda marca de tarea, merma e incidencia la asigna el servidor; no se edita manualmente.
- **Solo lectura en MCP:** el servidor MCP nunca escribe en la base; las escrituras (marcas de tareas, mermas, incidencias) las realiza el backend.
- **Sin sanciones:** presenta hechos (qué tarea, quién la marcó, a qué hora), pero no juzga culpables ni propone sanciones; esas decisiones son del supervisor de turno o del jefe de tienda.
- **Privacidad (Ley N.° 29733):** cada usuario ve solo lo que su rol permite; no se exponen DNI, teléfonos ni correos del personal; en desarrollo solo se usan datos simulados.
- **Seguridad:** la clave de la API de Gemini vive en una variable de entorno y nunca en el código ni en el repositorio.
- **Datos simulados:** ninguna información real de Vivanda.

## Fuera de alcance (semestre 2026-II)
Integración con sistemas reales de Vivanda (compras, ventas, inventario real), sensores IoT físicos, pedidos o reposición automática a proveedores, creación o modificación de horarios, planilla y pagos del personal, aplicación móvil nativa y escritura en la base de datos mediante el servidor MCP.