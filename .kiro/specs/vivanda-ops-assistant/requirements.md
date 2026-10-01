# Requirements Document

## Introduction

El **Asistente Inteligente de Operaciones para Tienda Vivanda** es un sistema de software que permite al personal operativo de las tiendas Vivanda gestionar el control diario de turno a través de una interfaz web conversacional impulsada por inteligencia artificial. El sistema reemplaza los avisos verbales y las planillas físicas firmadas al final del turno por un registro digital inmediato de incidencias, mermas, validaciones de checklist y consultas de inventario. Todos los datos de trabajadores e inventario son simulados; no se emplea ningún dato personal real, conforme a la Ley N.° 29733 de Protección de Datos Personales del Perú. El sistema consume, en modo exclusivo de solo lectura, el servidor MCP construido en la semana 4 del curso sobre la base de datos del supermercado.

---

## Glossary

- **Asistente**: El agente de inteligencia artificial conversacional que procesa los mensajes del personal y ejecuta acciones sobre la base de datos local.
- **Base_de_Datos**: La base de datos SQLite local del sistema que almacena incidencias, mermas, checklists y reportes de turno.
- **Servidor_MCP**: El servidor Model Context Protocol construido en la semana 4, utilizado únicamente en modo de solo lectura para consultar inventarios, órdenes y reportes del supermercado.
- **Interfaz_Web**: La aplicación frontend desarrollada en HTML y JavaScript que permite la interacción del personal con el Asistente mediante mensajes de texto.
- **Gestor_de_Incidencias**: El módulo del sistema responsable de registrar, almacenar y recuperar incidencias operativas, mermas y validaciones de checklist.
- **Generador_de_Reportes**: El módulo del sistema responsable de consolidar y producir el reporte de cierre de turno en formato de texto.
- **Turno**: El período laboral de un miembro del personal, identificado por el usuario autenticado, la fecha y la hora de inicio.
- **Merma**: Pérdida de un producto del inventario ocasionada por daño, vencimiento, robo o cualquier causa que lo hace no apto para la venta.
- **Incidencia**: Cualquier evento operativo relevante que debe quedar registrado durante el turno, incluyendo mermas, problemas de infraestructura u otras situaciones anómalas.
- **Checklist**: Lista de verificación de tareas operativas correspondiente a la apertura o cierre de turno.
- **Reporte_de_Cierre**: Documento de texto generado al final del turno que consolida todas las incidencias, mermas y validaciones registradas durante el turno.
- **Jefe_de_Tienda**: Rol del personal con acceso completo a reportes, incidencias y consultas del sistema.
- **Supervisor_de_Turno**: Rol del personal responsable de validar los checklists de apertura y cierre de turno.
- **Reponedor**: Rol del personal responsable de registrar mermas y consultar información de inventario en piso.
- **Encargado_de_Recepcion**: Rol del personal responsable de verificar la recepción de mercadería contra las órdenes de proveedores.

---

## Requirements

### Requisito 1: Interfaz web conversacional para interacción del personal

**Historia de usuario:** Como miembro del personal de tienda, quiero interactuar con el asistente a través de una interfaz web de chat, para poder registrar eventos operativos y consultar información sin necesidad de planillas físicas.

#### Criterios de aceptación

1. THE Interfaz_Web SHALL presentar un área de entrada de texto y un historial de conversación accesibles desde cualquier navegador web compatible con HTML5 y JavaScript sin instalación adicional.
2. WHEN el usuario envía un mensaje de texto de hasta 1000 caracteres a través de la Interfaz_Web, THE Asistente SHALL procesar el mensaje y devolver una respuesta en un plazo máximo de 10 segundos.
3. WHEN el usuario inicia sesión en la Interfaz_Web y existe un turno activo, THE Asistente SHALL asociar todas las acciones de la sesión al usuario autenticado y al turno activo correspondiente.
4. IF el Asistente no logra asociar el mensaje a ninguna de las operaciones de tienda (mermas, checklist, órdenes, inventario o cierre), THEN THE Asistente SHALL responder con un mensaje de ayuda que liste las acciones disponibles.
5. WHILE el usuario mantiene una sesión activa, THE Interfaz_Web SHALL conservar el historial completo de la conversación del turno actual, permitiendo desplazamiento vertical cuando el historial supera el área visible en pantalla.
6. IF la conexión con el Servidor_MCP no está disponible, THEN THE Asistente SHALL notificar al usuario que las consultas de inventario no están disponibles en ese momento y registrar el evento en la Base_de_Datos.
7. IF el usuario inicia sesión en la Interfaz_Web y no existe un turno activo, THEN THE Asistente SHALL notificar al usuario que no hay un turno activo y solicitar que inicie un turno antes de realizar cualquier acción de registro.

---

### Requisito 2: Registro de mermas y productos en mal estado

**Historia de usuario:** Como reponedor, quiero registrar productos en mal estado o mermas, para dejar constancia inmediata de la pérdida en el inventario del turno actual.

#### Criterios de aceptación

1. WHEN el Reponedor ingresa el código de producto y el detalle de la merma en la Interfaz_Web, THE Gestor_de_Incidencias SHALL guardar el registro de la merma asociado al usuario, turno, fecha y hora exacta del ingreso.
2. THE Gestor_de_Incidencias SHALL registrar para cada merma los campos obligatorios: código de producto, descripción del daño o causa de la merma (entre 10 y 500 caracteres), cantidad afectada (entero positivo mayor que cero), usuario que reporta y turno activo.
3. IF el código de producto ingresado no existe en la Base_de_Datos local ni en el Servidor_MCP, THEN THE Gestor_de_Incidencias SHALL rechazar el registro e informar al usuario que el código no fue encontrado.
4. WHEN una merma es registrada exitosamente, THE Gestor_de_Incidencias SHALL confirmar al usuario el registro con un resumen que incluya: código de producto, descripción, cantidad afectada, usuario y turno almacenados.
5. IF el campo de cantidad afectada contiene un valor no numérico, decimal o menor o igual a cero, THEN THE Gestor_de_Incidencias SHALL rechazar el registro e informar al usuario que la cantidad debe ser un número entero positivo mayor que cero.
6. THE Gestor_de_Incidencias SHALL permitir el registro de múltiples mermas durante un mismo turno sin restricción de cantidad de registros.

---

### Requisito 3: Validación de checklists de apertura y cierre de turno

**Historia de usuario:** Como supervisor de turno, quiero validar el checklist de tareas de apertura o cierre de tienda, para asegurar que el local cumple con las condiciones operativas óptimas antes de abrir o después de cerrar.

#### Criterios de aceptación

1. WHEN el Supervisor_de_Turno solicita el checklist de apertura o cierre a través de la Interfaz_Web, THE Asistente SHALL presentar la lista completa de ítems de verificación correspondientes al tipo de turno activo, mostrando para cada ítem su identificador, descripción y estado (pendiente o completado).
2. WHEN el Supervisor_de_Turno confirma la validación de un ítem del checklist, THE Gestor_de_Incidencias SHALL registrar el ítem como completado en un plazo máximo de 3 segundos, incluyendo el identificador del ítem, el identificador del Supervisor_de_Turno, la fecha en formato YYYY-MM-DD y la hora exacta en formato HH:MM:SS de la confirmación.
3. WHEN el Supervisor_de_Turno completa la validación de todos los ítems del checklist, THE Gestor_de_Incidencias SHALL registrar el checklist como completado en un plazo máximo de 3 segundos, incluyendo el identificador de la sesión de turno, el identificador del Supervisor_de_Turno y la hora de cierre en formato HH:MM:SS.
4. IF el Supervisor_de_Turno intenta registrar una validación de checklist sin una sesión de turno activa, THEN THE Gestor_de_Incidencias SHALL rechazar la operación e indicar al usuario que debe iniciar un turno antes de validar el checklist, sin registrar ningún cambio de estado en los ítems.
5. THE Gestor_de_Incidencias SHALL almacenar el estado de cada ítem del checklist (pendiente o completado) de forma independiente y persistente, de modo que una interrupción de sesión no revierta el progreso de los ítems previamente confirmados.
6. WHEN el Supervisor_de_Turno solicita ver el estado del checklist en curso, THE Asistente SHALL mostrar en un plazo máximo de 3 segundos el listado de ítems completados con su hora de confirmación y el listado de ítems pendientes de la sesión de turno activa.
7. IF el Supervisor_de_Turno intenta confirmar un ítem que ya figura como completado en la sesión de turno activa, THEN THE Gestor_de_Incidencias SHALL rechazar la operación e indicar al usuario que el ítem ya fue validado, sin modificar el registro existente del ítem.

---

### Requisito 4: Verificación de recepción de mercadería contra orden del proveedor

**Historia de usuario:** Como encargado de recepción, quiero verificar la mercadería recibida contra la orden oficial del proveedor, para evitar aceptar diferencias o productos faltantes durante el proceso de recepción.

#### Criterios de aceptación

1. WHEN el Encargado_de_Recepcion solicita validar una entrega identificando el número de orden, THE Asistente SHALL consultar al Servidor_MCP y mostrar los detalles de la orden oficial: proveedor, productos esperados con sus cantidades, fecha de emisión de la orden y fecha de entrega comprometida.
2. IF el número de orden ingresado no existe en el Servidor_MCP, THEN THE Asistente SHALL informar al Encargado_de_Recepcion que la orden no fue encontrada y sugerir verificar el número de documento.
3. THE Asistente SHALL utilizar el Servidor_MCP únicamente en modo de solo lectura; ninguna operación de recepción modificará los registros del Servidor_MCP.
4. IF el Servidor_MCP rechaza una operación por intento de escritura, THEN THE Asistente SHALL informar al usuario que la operación no está permitida y no realizará ninguna modificación en el Servidor_MCP.

---

### Requisito 5: Consulta de inventario y precios mediante el Servidor MCP

**Historia de usuario:** Como reponedor, quiero consultar el inventario disponible y los precios de los productos, para reponer correctamente las góndolas y asistir con información exacta a los clientes.

#### Criterios de aceptación

1. WHEN el Reponedor ingresa el nombre o código de un producto en la Interfaz_Web, THE Asistente SHALL consultar al Servidor_MCP y devolver la cantidad disponible en inventario (expresada en unidades enteras) y el precio actual del producto (expresado con dos decimales en la moneda local) dentro de los 8 segundos siguientes al envío de la consulta.
2. IF el producto consultado no existe en el Servidor_MCP, THEN THE Asistente SHALL informar al Reponedor que el producto no fue encontrado mediante un mensaje que indique el término buscado, y sugerir intentar con otro término de búsqueda, sin modificar ningún dato en el Servidor_MCP.
3. WHEN el Asistente devuelve resultados de inventario, THE Asistente SHALL indicar la fecha y hora de la consulta al Servidor_MCP en formato DD/MM/AAAA HH:MM:SS junto a cada resultado mostrado.
4. THE Asistente SHALL consultar al Servidor_MCP en modo de solo lectura; ninguna consulta de inventario modificará los datos del Servidor_MCP.
5. IF la consulta al Servidor_MCP no recibe respuesta en 8 segundos, THEN THE Asistente SHALL notificar al Reponedor que la consulta no obtuvo respuesta en el tiempo esperado, conservar el estado previo de la Interfaz_Web sin pérdida de datos ingresados, y ofrecer la opción de reintentar la consulta.
6. WHEN el Reponedor realiza una búsqueda de producto por nombre parcial con un término de al menos 2 caracteres, THE Asistente SHALL devolver todos los productos cuyo nombre contenga el término ingresado (sin distinción entre mayúsculas y minúsculas), con un máximo de 20 resultados por consulta ordenados alfabéticamente por nombre de producto.
7. IF el término de búsqueda por nombre parcial contiene menos de 2 caracteres, THEN THE Asistente SHALL informar al Reponedor que el término ingresado es demasiado corto e indicar que se requieren al menos 2 caracteres para realizar la búsqueda, sin enviar la consulta al Servidor_MCP.

---

### Requisito 6: Generación del reporte de cierre de turno

**Historia de usuario:** Como jefe de tienda, quiero acceder al reporte de cierre de turno con el resumen de mermas e incidencias, para conocer con precisión el balance operativo del día.

#### Criterios de aceptación

1. WHEN el Jefe_de_Tienda solicita el reporte de cierre de turno a través de la Interfaz_Web, THE Generador_de_Reportes SHALL consolidar todas las incidencias, mermas y validaciones de checklist registradas durante el turno activo.
2. WHEN el Jefe_de_Tienda solicita el reporte de cierre de turno a través de la Interfaz_Web, THE Generador_de_Reportes SHALL producir el reporte en un tiempo máximo de 10 segundos, en formato de texto usando viñetas (Markdown), incluyendo: identificador del turno, nombre del responsable, hora de inicio y de cierre, lista de mermas con el total de unidades dadas de baja, lista de incidencias con la cantidad total de incidencias registradas, y estado del checklist.
3. WHEN el Generador_de_Reportes produce el reporte de cierre, THE Base_de_Datos SHALL almacenar el reporte asociado al identificador del turno para consulta futura.
4. IF la Base_de_Datos no puede almacenar el reporte de cierre, THEN THE Generador_de_Reportes SHALL indicar al Jefe_de_Tienda mediante un mensaje de error que el reporte no pudo ser guardado, preservando el reporte generado disponible en la Interfaz_Web durante la sesión activa.
5. IF no existen registros de mermas, incidencias ni validaciones de checklist para el turno solicitado, THEN THE Generador_de_Reportes SHALL generar el reporte indicando explícitamente que no se registraron eventos durante el turno.
6. WHEN el Jefe_de_Tienda solicita el reporte de un turno ya cerrado, THE Generador_de_Reportes SHALL recuperar el reporte almacenado en la Base_de_Datos sin regenerarlo.
7. IF el Jefe_de_Tienda solicita el reporte de un identificador de turno que no existe en la Base_de_Datos, THEN THE Generador_de_Reportes SHALL mostrar un mensaje de error indicando que el turno no fue encontrado y no producirá ningún reporte.

---

### Requisito 7: Gestión de sesión de turno y autenticación de usuario

**Historia de usuario:** Como miembro del personal, quiero identificarme al iniciar mi turno en el sistema, para que todas las acciones queden registradas con mi usuario y el turno correspondiente.

#### Criterios de aceptación

1. WHEN un miembro del personal ingresa sus credenciales en la Interfaz_Web, THE Asistente SHALL validar las credenciales contra la Base_de_Datos y crear una sesión de turno activa asociada al usuario y la marca de tiempo de inicio.
2. IF las credenciales ingresadas no corresponden a un usuario registrado en la Base_de_Datos, THEN THE Asistente SHALL rechazar el acceso e informar al usuario que las credenciales son incorrectas.
3. THE Asistente SHALL asociar cada acción de registro (mermas, incidencias, checklists) al identificador de sesión de turno activa del usuario autenticado.
4. WHEN el miembro del personal cierra su sesión de turno, THE Asistente SHALL registrar la hora de cierre del turno y marcar la sesión como inactiva en la Base_de_Datos.
5. IF un usuario intenta realizar una acción de registro sin una sesión de turno activa, THEN THE Asistente SHALL rechazar la operación e indicar al usuario que debe iniciar sesión primero.
6. IF un usuario inicia sesión cuando ya existe una sesión activa para ese mismo usuario, THEN THE Asistente SHALL cerrar la sesión anterior registrando su marca de tiempo de cierre, crear una nueva sesión activa e informar al usuario que la sesión anterior fue cerrada.

---

### Requisito 8: Registro general de incidencias operativas

**Historia de usuario:** Como supervisor de turno, quiero registrar incidencias operativas generales (problemas de infraestructura, equipos, clima, etc.) durante el turno, para mantener un historial completo de eventos que afecten la operación de la tienda.

#### Criterios de aceptación

1. WHEN el Supervisor_de_Turno describe una incidencia operativa en la Interfaz_Web, THE Gestor_de_Incidencias SHALL registrar la incidencia con los campos: descripción (entre 10 y 1000 caracteres), categoría (seleccionada de la lista de valores válidos), usuario que reporta, turno activo y marca de tiempo con fecha y hora exactas del momento de registro.
2. THE Gestor_de_Incidencias SHALL soportar exactamente las siguientes categorías de incidencia: infraestructura, equipamiento, abastecimiento, seguridad, clima.
3. IF la descripción de la incidencia ingresada tiene menos de 10 caracteres o más de 1000 caracteres, THEN THE Gestor_de_Incidencias SHALL rechazar el registro e indicar al usuario que la descripción no cumple con el largo requerido (entre 10 y 1000 caracteres).
4. IF el Supervisor_de_Turno no selecciona una categoría válida al registrar la incidencia, THEN THE Gestor_de_Incidencias SHALL rechazar el registro e indicar al usuario que la categoría es obligatoria y debe ser una de las categorías soportadas.
5. WHEN una incidencia es registrada exitosamente, THE Gestor_de_Incidencias SHALL confirmar al usuario con un identificador único de la incidencia y un resumen de los datos guardados dentro de los 3 segundos siguientes al envío.
6. WHEN el Supervisor_de_Turno solicita el listado de incidencias del turno activo, THE Gestor_de_Incidencias SHALL devolver todas las incidencias registradas durante el turno ordenadas por hora de registro descendente, mostrando al menos: identificador, categoría, descripción y hora de registro de cada incidencia.
7. IF no existe un turno activo al momento de registrar una incidencia, THEN THE Gestor_de_Incidencias SHALL rechazar el registro e indicar al usuario que no hay un turno activo en curso.

---

### Requisito 9: Seguridad de credenciales (RNF)

**Historia de usuario:** Como administrador del sistema, quiero proteger las claves de acceso a los servicios externos para evitar su exposición en el código fuente.

### Requisito 10: Privacidad y datos simulados (RNF)
**Historia de usuario:** Como responsable del cumplimiento legal, quiero asegurar que el sistema opere sin información sensible para respetar la Ley N.° 29733.
#### Criterios de aceptación
1. THE Backend SHALL NO almacenar, procesar ni exponer datos personales reales de los trabajadores en ningún módulo, restringiendo su operación exclusivamente a datos simulados.

#### Criterios de aceptación

1. THE Backend SHALL mantener toda clave de API, incluyendo GEMINI_API_KEY, fuera del código fuente, alojándola exclusivamente en variables de entorno.
