# ADR-001: Framework de aplicación

**Proyecto:** APK-TUTI 2.0 · Asistente Inteligente de Operaciones para Tienda Vivanda
**Tecnologías fijadas por la actividad:** PHP 8.x y MySQL sobre XAMPP
**Relacionados:** ADR-002 (base de datos y persistencia) y ADR-003 (repositorio y ramas), que redacta el equipo.

## Estado

**Propuesto.** Borrador de la IA; el equipo decide y los cuatro integrantes firman. El ADR que se versiona lleva la decisión del equipo, no la de la IA.

## Contexto

Drivers que condicionan la decisión:

- **Seguridad y privacidad (alta):** acceso por rol sin exponer DNI, teléfonos ni correos (Req. 10); claves fuera del repositorio (Req. 9).
- **Fiabilidad (alta):** los ítems de checklist confirmados sobreviven a una interrupción (HU-3, CA 5); sin turno activo se rechaza todo registro (HU-7, CA 5).
- **Eficiencia (media):** ≤3 s al confirmar un ítem (HU-3, CA 2), ≤8 s en consultas de inventario (HU-5, CA 1) y ≤10 s en el reporte de cierre (HU-6, CA 2).
- **Restricciones:** equipo de cuatro, aplicación funcional, probada y desplegada en las semanas 7 y 8, cuentas gratuitas, y soluciones simples y sin sobreingeniería (tech.md).
- **Alcance:** el agente conversacional, los modelos de IA y la conexión MCP quedan fuera de esta semana; solo se deja visible el punto de integración.

**[CONFLICTO]:** tech.md y requirements.md del repositorio indican otra pila (Python y SQLite). Prevalece la indicada por la actividad: PHP y MySQL.

## Estilo arquitectónico (decisión previa)

Se compararon tres estilos con los drivers anteriores (puntaje 1–5; total ponderado: alta ×3, media ×2).

| Driver | Prioridad (peso) | (a) MVC en capas | (b) Modular por dominio | (c) API REST + SPA |
|---|---|---|---|---|
| Seguridad y privacidad | Alta ×3 | 4 · Sesión y roles resueltos en servidor. | 4 · Roles aislables por módulo. | 3 · Cada endpoint exige autenticación; tokens en cliente. |
| Fiabilidad | Alta ×3 | 4 · Validación y persistencia en un solo flujo. | 4 · Cada módulo valida sus propias reglas. | 3 · Cliente y API deben sincronizar estado. |
| Eficiencia | Media ×2 | 4 · Render directo; ≤3, 8 y 10 s alcanzables. | 4 · Mismo costo de ejecución que (a). | 3 · Más solicitudes; cumple, con menor margen. |
| Plazo y equipo (semanas 7–8, cuatro integrantes) | Alta ×3 [INFERIDO] | 5 · Un solo despliegue; poca coordinación. | 3 · Exige acordar límites de módulos. | 2 · Dos aplicaciones, contrato de API, doble prueba. |
| Técnica: PHP y MySQL en XAMPP | Alta ×3 [INFERIDO] | 5 · Corre en XAMPP sin componentes adicionales. | 5 · Igual que (a). | 4 · Requiere servir dos aplicaciones en XAMPP. |
| Riesgo: turno definido tarde (HU-7) | Media ×2 [INFERIDO] | 3 · El cambio se dispersa entre capas. | 5 · Turno encapsulado; cambio localizado. | 3 · El cambio toca API y cliente. |
| **Total ponderado (máx. 80)** | | **68** | **66** | **48** |

**Recomendación:** adoptar (a), MVC en capas con renderizado en servidor. (a) y (b) quedan a 2 puntos, un empate práctico; el desempate es el plazo de las semanas 7 y 8 con cuatro integrantes, donde (a) es más simple. Condición de revisión: si antes de la semana 7 un cambio en HU-7 o HU-8 obliga por segunda vez a modificar varias capas, pasar a (b) [INFERIDO].

**Punto de integración futura del agente (Unidad 3):** dejar un punto visible, vacío y sin implementar entre la interfaz de chat (HU-1) y los servicios de registro de mermas, checklists e incidencias (HU-2, 3, 8), para que el agente de la Unidad 3 use los mismos registros. La consulta al MCP queda aparte, de solo lectura (HU-4, HU-5). Nada se diseña esta semana.

## Alternativas de framework

MySQL ya está fijado; se evalúan solo frameworks de PHP 8 compatibles con el estilo (a).

| Alternativa | Ventajas aquí | Desventajas aquí | Instalación en XAMPP |
|---|---|---|---|
| **Laravel** | Autenticación, validación y roles incluidos; ayuda a seguridad y HU-7. | Curva más larga para dos semanas; muchas convenciones. | Requiere gestor de paquetes y PHP compatible [VERIFICAR]. |
| **CodeIgniter 4** | Ligero, curva corta; validación incluida; encaja con la simplicidad pedida. | Autenticación y roles requieren trabajo propio o paquete adicional. | Instalación manual o con gestor; extensiones de PHP requeridas [VERIFICAR]. |
| **PHP 8 con MVC propio** | Sin dependencias; control total. | El equipo escribe rutas, sesión y validación: riesgo para seguridad y plazo. | Solo XAMPP; reescritura de URL [VERIFICAR]. |

## Decisión

**Propuesta de la IA: Laravel**, versión estable vigente [VERIFICAR].

Laravel y CodeIgniter 4 empatan en este contexto: Laravel favorece la seguridad; CodeIgniter 4, el plazo. **Desempate:** el equipo ya registró Laravel como propuesta en su archivo de stack, y la autenticación, la validación y las migraciones incluidas reducen el riesgo en los dos atributos de prioridad alta. Si el equipo no lo domina o no se instala en los cuatro equipos [VERIFICAR], se usa CodeIgniter 4. Se descarta el MVC propio por riesgo de seguridad.

| Registro de decisión del equipo | |
|---|---|
| **Framework elegido** | |
| **Si difiere de la propuesta, motivo (obligatorio)** | |
| **Fecha** | |
| **Firmas (cuatro integrantes)** | 1. __________ 2. __________ 3. __________ 4. __________ |

## Consecuencias y deuda aceptada

| Tipo | Detalle |
|---|---|
| Positiva | Estructura común para los cuatro; autenticación, validación y migraciones incluidas; menos código propio. |
| Negativa | Dependencia del framework y de su versión estable vigente; curva de aprendizaje durante dos semanas. |
| Negativa | Las tablas en singular (convención del equipo) exigen declarar el nombre de tabla en cada modelo, porque el framework usa plural por defecto. |
| Deuda aceptada | Roles y control de turno configurados por el equipo; integración del agente (Unidad 3) pendiente. |

## Criterio de reversión

Antes de la semana 7, revertir a CodeIgniter 4 si Laravel no corre en los cuatro XAMPP [VERIFICAR] o si HU-7 (sesión y turno) no funciona de extremo a extremo [INFERIDO].
