---
inclusion: always
---
# Stack y reglas técnicas · APK-TUTI 2.0, asistente inteligente de operaciones de tienda Vivanda

Redacta todos los documentos y respuestas en español.

## Decisiones vigentes
- Producto: asistente de operaciones que actúa como supervisor virtual del personal de una tienda Vivanda y centraliza checklists, mermas e incidencias. Proyecto universitario de un equipo de cuatro integrantes.
- Por indicación de la actividad, PHP y MySQL prevalecen sobre cualquier otra pila mencionada en documentos anteriores del repositorio.
- Framework: Laravel, versión estable vigente [VERIFICAR]. Elegido por autenticación y roles, validación y migraciones incluidos. ADR-001 pendiente de firma del equipo. Alternativa de reversión: CodeIgniter 4.
- Lenguaje: PHP 8.x, según la versión incluida en el XAMPP instalado; debe ser compatible con la versión estable vigente de Laravel [VERIFICAR].
- Base de datos: MySQL/MariaDB de XAMPP; motor InnoDB; collation utf8mb4_unicode_ci. El esquema se crea y modifica solo mediante migraciones.
- Vistas: plantillas Blade con renderizado en servidor; HTML, CSS y JavaScript simple, sin frameworks pesados [VERIFICAR].
- Gestor de paquetes: Composer [VERIFICAR instalación en XAMPP].
- Servidor: Apache de XAMPP; raíz pública: carpeta public/ de Laravel [VERIFICAR VirtualHost].
- Herramientas: Visual Studio Code y Kiro IDE; solo servicios con nivel gratuito.
- Control de versiones: GitHub; ramas main (estable), develop (integración), feature-<modulo> (trabajo).
- Plazo: aplicación funcional, probada y desplegada en las semanas 7 y 8.

## Convenciones
- Nombres de dominio, rutas, tablas y vistas en español.
- Base de datos: snake_case y singular (merma, incidencia, sesion_turno). Laravel asume tablas en plural, por lo que cada modelo declara su tabla con protected $table.
- Clases: PascalCase. Modelos en singular (Merma, Incidencia); controladores con sufijo Controller (MermaController).
- Rutas: kebab-case (/registro-de-mermas, /cierre-de-turno).
- Commits en español con Conventional Commits (feat:, fix:, docs:, chore:).

## Reglas inviolables
- Secretos (claves de API y credenciales de servicios externos) únicamente en .env; .env nunca se versiona (se versiona .env.example sin valores).
- Todo módulo nuevo traza a una historia de .kiro/specs/vivanda-ops-assistant/requirements.md; si no traza, no se construye.
- Toda conclusión sin respaldo explícito en los documentos se marca [INFERIDO]; si dos fuentes discrepan, se marca [CONFLICTO]. No se inventan requisitos, métricas, normas ni decisiones.
- Toda tabla nueva o modificada se hace mediante migración y obliga a actualizar docs/arquitectura/modelo_datos.md en el mismo commit.
- Solo datos simulados; ningún dato personal real de trabajadores se almacena, procesa ni expone (Ley N.º 29733).
- Acceso por rol (Jefe de Tienda, Supervisor de Turno, Reponedor, Encargado de Recepción): cada usuario ve solo lo que su rol permite; no se exponen DNI, teléfonos ni correos del personal. Contraseñas con hash.
- La fecha y hora de toda marca de checklist, merma e incidencia las asigna el servidor, no el cliente.
- Soluciones simples y legibles, de nivel universitario; sin sobreingeniería.

## FRONTERA DE ALCANCE
- No implementar el agente conversacional, modelos de IA ni conexión MCP hasta la Unidad 3.
- El servidor MCP existente es de solo lectura; las escrituras (marcas de checklist, mermas, incidencias) las realiza el backend.
- La carpeta mcp_server/ (Unidad 1, Python y SQLite) no se modifica en esta etapa. Cualquier conexión con ella se decide en la Unidad 3, no antes.
- Existe el módulo asistente/ como punto de extensión vacío y documentado [VERIFICAR ubicación dentro de la estructura de Laravel]; cualquier código de IA dentro de él antes de la Unidad 3 es un error.