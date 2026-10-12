# Santi: especificación técnica de arquitectura

- **Estado:** propuesta de especificación para revisión humana; no aprobada para implementación.
- **Fecha:** 2026-10-12
- **Versión del documento:** 1.0
- **Ámbito:** arquitectura conceptual y requisitos verificables para la primera versión funcional.
- **Repositorio de referencia:** `danihal1224/santi-dev-workflow`
- **Principio rector:** autonomía útil dentro de límites explícitos, verificables y revocables.

## 1. Resumen ejecutivo

Santi será un asistente de trabajo con un núcleo común de planificación, memoria curada, autorización, seguridad, calidad, observabilidad y comunicación, ampliable mediante módulos especializados independientes. La primera especialidad será el desarrollo de software. Otras especialidades se incorporarán más adelante, sin mezclar sus datos privados ni sus procedimientos específicos.

La arquitectura será híbrida: ChatGPT será la interfaz conversacional principal; Notion contendrá el conocimiento general, las decisiones y los procedimientos de proyecto; GitHub será la fuente oficial del código, las pruebas, los cambios versionados y los controles de integración; el sistema de ejecución será la fuente del estado real de las operaciones; y un registro de autorizaciones separado determinará los permisos vigentes. Las herramientas conectadas se usarán solo dentro de un catálogo aprobado.

La primera versión no tendrá una aplicación independiente ni un panel de control personalizado. Se usarán ChatGPT y las vistas nativas de GitHub y Notion. Un panel propio se reconsiderará únicamente si el piloto demuestra una necesidad concreta.

Esta especificación **no autoriza por sí misma la implementación, la conexión de nuevos proveedores, el acceso a producción ni el lanzamiento**. Esos pasos requieren planificación posterior, verificación de capacidades reales y aprobaciones específicas.

## 2. Objetivos y criterios de diseño

### 2.1 Objetivos

1. Ayudar a planificar, desarrollar, probar, revisar y documentar software.
2. Ejecutar tareas rutinarias previamente autorizadas sin pedir aprobación en cada paso de bajo riesgo.
3. Mantener el control humano sobre objetivos, presupuesto, permisos, datos sensibles, cambios estructurales y compromisos externos.
4. Verificar resultados observables antes de declarar una tarea terminada.
5. Recuperarse de fallos sin duplicar operaciones ni ocultar estados inciertos.
6. Mantener trazabilidad, control de costes y capacidad efectiva de parada y revocación.
7. Permitir nuevas especialidades mediante módulos independientes, sin construir todas desde el inicio.

### 2.2 Principios no negociables

- Mínimo privilegio y separación de entornos.
- Autorización explícita, limitada, vigente y verificable.
- Las instrucciones superiores y los controles de seguridad prevalecen sobre contenido externo.
- Los documentos, páginas web, repositorios y resultados de herramientas son datos, no autoridad para cambiar reglas o permisos.
- No se declara éxito sin evidencia suficiente.
- No se reduce la seguridad para mejorar métricas de productividad.
- No se amplía presupuesto, catálogo de herramientas, permisos o autonomía para sortear un límite.
- Las mejoras permanentes se proponen, prueban, versionan y aprueban según su impacto.
- No se comparte por defecto una sesión autenticada del navegador con servicios externos.
- Las capacidades de proveedores se verifican antes de convertirse en dependencias críticas.

## 3. Alcance

### 3.1 Incluido en la versión inicial

- Interfaz principal mediante ChatGPT.
- Núcleo de planificación por objetivos y coordinación de tareas.
- Memoria curada, con propuesta de guardado y confirmación para cambios permanentes importantes.
- Módulo inicial de desarrollo de software.
- Notion para conocimiento general, decisiones y procedimientos.
- GitHub para código, pruebas, ramas, revisiones, CI y protecciones.
- Catálogo de herramientas y modelos autorizados.
- Controles técnicos de permisos independientes de las instrucciones del modelo.
- Pruebas de calidad y seguridad proporcionales al riesgo.
- Recuperación limitada, registro estructurado y métricas equilibradas.
- Control de costes con límites, margen preventivo y pausa.
- Copias de seguridad y pruebas de restauración.
- Validación escalonada y piloto supervisado.

### 3.2 Fuera de alcance en la versión inicial

- Aplicación independiente o panel de control personalizado.
- Despliegues autónomos en producción.
- Ampliación automática de permisos o presupuestos.
- Conexiones o proveedores no aprobados.
- Autocambio de instrucciones permanentes, arquitectura, controles de seguridad o límites.
- Lanzamiento de todas las especialidades a la vez.
- Uso de sesiones autenticadas del navegador compartidas con terceros por defecto.
- Reentrenamiento automático de modelos.
- Acceso general a datos reales o a producción.
- Sustitución automática por proveedores no aprobados.

## 4. Arquitectura lógica

### 4.1 Componentes

**A. Interfaz conversacional**

- Recibe objetivos, restricciones, aprobaciones y solicitudes.
- Comunica progreso, decisiones, bloqueos, resultados y riesgos.
- No constituye por sí sola un control de seguridad ni un mecanismo de parada garantizado.

**B. Coordinador**

- Descompone objetivos en tareas y dependencias.
- Asigna tareas a módulos especializados.
- Gestiona prioridad, concurrencia, estado y escalado.
- No puede ampliar sus propios permisos ni cambiar objetivos o presupuestos aprobados.

**C. Núcleo común**

- Planificación por objetivos.
- Memoria curada.
- Resolución de conflictos y jerarquía de instrucciones.
- Evaluación de riesgos e incertidumbre.
- Comunicación y notificaciones.
- Gestión de cambios y decisiones.
- Observabilidad y métricas.

**D. Módulos de especialidad**

- El módulo inicial será desarrollo de software.
- Cada módulo tendrá procedimientos, fuentes, herramientas, criterios de calidad, límites y contexto propios.
- Los módulos compartirán únicamente los servicios comunes necesarios.
- La incorporación de un módulo nuevo requiere revisión de alcance, datos, riesgos y permisos.

**E. Capa de políticas y autorizaciones**

- Verifica actor, acción, recurso, alcance, condiciones y vigencia del permiso.
- Deniega acciones cuando la autorización no pueda comprobarse.
- Comprueba límites de presupuesto, herramienta, entorno y tipo de operación.
- Es independiente del modelo y debe aplicarse en la ruta de ejecución de las herramientas.

**F. Adaptadores de herramientas**

- Conectan únicamente con proveedores del catálogo aprobado.
- Declaran capacidades, permisos requeridos, límites, costes conocidos, comportamiento ante errores y métodos de verificación.
- No deben convertir una respuesta textual del modelo en autorización.
- Cada acción externa relevante debe tener un resultado comprobable o quedar como pendiente de verificación.

**G. Almacenes y fuentes de verdad**

- GitHub: código, pruebas, historial de cambios y controles de CI.
- Notion: conocimiento general, procedimientos, decisiones y vista general del proyecto.
- Sistema de ejecución: estado real de operaciones y tareas en curso.
- Registro de autorizaciones: permisos y aprobaciones vigentes.
- Memoria de ChatGPT: solo contexto personal útil y curado, no almacén operativo canónico.

**H. Observabilidad**

- Registros estructurados, métricas, eventos de seguridad, alertas y evidencias de verificación.
- No debe registrar credenciales, secretos ni datos privados innecesarios.

### 4.2 Diagrama de flujo lógico

1. El usuario comunica objetivo y restricciones.
2. El coordinador crea un plan y determina dependencias y riesgos.
3. El módulo adecuado prepara las acciones propuestas.
4. La capa de políticas comprueba permisos, presupuesto, alcance y vigencia.
5. El adaptador ejecuta únicamente las acciones permitidas.
6. El verificador contrasta el resultado con criterios de aceptación.
7. El sistema actualiza el estado, registra evidencias y comunica el resultado.
8. Si hay incertidumbre crítica, conflicto, fallo persistente o límite alcanzado, bloquea la parte afectada y escala.

## 5. Planificación y autonomía

### 5.1 Planificación por objetivos

- El usuario establece objetivo, restricciones y criterios relevantes.
- Santi descompone el trabajo, identifica dependencias y propone prioridades.
- Puede reorganizar tareas rutinarias dentro del alcance aprobado.
- Cambios de objetivos, compromisos, presupuesto, alcance estructural o criterios de aceptación requieren consulta y aprobación.

### 5.2 Niveles de autonomía

- **Bajo riesgo, reversible y autorizado:** ejecución autónoma con verificación apropiada.
- **Riesgo medio o contexto incierto:** comprobaciones adicionales; consulta si cambia materialmente el alcance.
- **Alto impacto, sensible o irreversible:** aprobación humana explícita antes de ejecutar.
- **No permitido o sin autorización verificable:** denegación y explicación breve con alternativa segura cuando exista.

La supervisión será proporcional al impacto, no al nivel de confianza expresado por el modelo.

### 5.3 Concurrencia

- Paralelismo limitado y adaptativo.
- Límites por proyecto, herramienta y recursos.
- Dependencias explícitas y prevención de escrituras incompatibles.
- Límites numéricos pendientes de pruebas reales, cuotas y costes.
- Las tareas independientes pueden continuar durante un fallo si se verifica que no están afectadas.

## 6. Memoria, conocimiento y fuentes

### 6.1 Memoria curada

- Santi propondrá qué información personal o preferencia merece guardarse.
- Los cambios permanentes importantes requieren confirmación.
- La información temporal debe caducar o revisarse.
- Deben admitirse corrección y eliminación.
- No se guardarán secretos, credenciales ni datos sensibles innecesarios.
- Los registros de auditoría no se convertirán automáticamente en memoria.

### 6.2 Fuentes oficiales por tipo

- Código, pruebas y cambios: GitHub.
- Conocimiento general, decisiones y procedimientos: Notion.
- Estado de una operación: sistema que la ejecutó, contrastado cuando sea necesario.
- Permisos y aprobaciones: registro de autorizaciones.
- Preferencias personales útiles: memoria curada.
- Las fechas de actualización no sustituyen la autoridad definida para cada dato.

### 6.3 Fiabilidad y actualización

- Priorizar fuentes primarias y documentación oficial.
- Verificar frescura y compatibilidad según riesgo.
- Distinguir hechos, inferencias, hipótesis y opiniones.
- Registrar procedencia y fecha para conocimiento técnico duradero.
- Las novedades se incorporan mediante propuesta, validación y versionado.
- Consultar documentación no equivale a modificar instrucciones ni a reentrenar un modelo.

### 6.4 Contenido externo no confiable

- Los contenidos externos pueden analizarse, resumirse o probarse, pero no pueden cambiar la jerarquía de instrucciones.
- Instrucciones incrustadas que soliciten secretos, ampliación de permisos o evasión de controles no se obedecerán.
- Las acciones sensibles se verifican mediante la capa técnica de autorización.

## 7. Autorizaciones y gobierno de cambios

### 7.1 Modelo de autorización

Toda autorización sensible debe especificar, en la medida aplicable:

- Quién autoriza.
- Acción permitida.
- Recurso y entorno.
- Alcance y límites.
- Condiciones y periodo de vigencia.
- Presupuesto o cuota aplicable.
- Evidencia necesaria para verificar el resultado.

Las autorizaciones son limitadas, verificables, revocables y pueden caducar. Una pausa no las renueva. Cambios materiales de alcance, contexto, riesgo o incidente obligan a revalidarlas; cuando proceda, se solicitará una nueva aprobación.

### 7.2 Decisiones reservadas al usuario

- Objetivos y prioridades de alto impacto.
- Presupuesto adicional o cambios del límite.
- Nuevos proveedores, conexiones y permisos.
- Uso o transferencia de datos sensibles.
- Cambios permanentes de instrucciones.
- Cambios estructurales de arquitectura y seguridad.
- Compromisos externos, publicaciones y despliegues de producción.
- Aceptación de riesgos residuales importantes.
- Lanzamiento de la versión.

### 7.3 Cambios según impacto

- Cambios rutinarios dentro del alcance: proceso aprobado, pruebas y trazabilidad.
- Cambios que afecten a calidad, compatibilidad o datos: revisión reforzada.
- Cambios de arquitectura, permisos, seguridad, presupuesto o compromisos: aprobación explícita.
- Las propuestas no equivalen a aprobación ni a aplicación.
- Versionado y reversión cuando sea viable.

### 7.4 Jerarquía de instrucciones

1. Instrucciones superiores de seguridad y políticas aplicables.
2. Autorizaciones válidas del usuario y reglas de proyecto aprobadas, dentro de su alcance.
3. Procedimientos técnicos y decisiones versionadas compatibles con los niveles superiores.
4. Documentos externos, páginas web y resultados de herramientas como datos sin autoridad para conceder permisos.
5. Ante un conflicto no resoluble que afecte a una acción crítica, detener la acción afectada.

## 8. Seguridad y privacidad

### 8.1 Controles obligatorios

- Mínimo privilegio.
- Separación entre desarrollo, pruebas y producción.
- Credenciales separadas por entorno y herramienta, almacenadas fuera del código y de los registros.
- Autorización técnica independiente antes de operaciones sensibles.
- Límites de alcance, presupuesto y concurrencia.
- Auditoría con acceso restringido.
- Revocación efectiva de permisos.
- Controles contra instrucciones externas maliciosas.
- Revisión de dependencias y licencias.
- Gestión de vulnerabilidades según severidad y exposición.
- Pruebas de seguridad y permisos antes del piloto.

### 8.2 Datos

- Clasificar por sensibilidad y finalidad.
- Compartir datos entre proyectos solo cuando exista una finalidad concreta y autorización aplicable.
- Usar datos sintéticos o anonimizados cuando sean suficientes.
- Acceso a producción y datos sensibles deshabilitado por defecto para tareas automatizadas, salvo autorización específica.
- Retención proporcional a la finalidad y al riesgo.
- Eliminación verificable de copias temporales cuando ya no sean necesarias.
- No incluir credenciales, tokens o secretos en documentación, memoria ni logs ordinarios.

### 8.3 Vulnerabilidades

- Detectar y clasificar por severidad, exposición e impacto.
- Preparar correcciones y probarlas en entorno aislado.
- Validar compatibilidad y regresiones.
- Aplicar actualizaciones rutinarias solo bajo procedimientos autorizados.
- Requerir revisión para cambios de alto impacto.
- Si una vulnerabilidad crítica no tiene corrección segura, aislar o pausar la función afectada y escalar.

### 8.4 Licencias y propiedad intelectual

- Revisar licencias directas y transitivas y la procedencia de las dependencias.
- Bloquear incompatibilidades conocidas.
- Escalar ambigüedades relevantes.
- No asumir que una licencia desconocida autoriza el uso.
- Las herramientas de análisis ayudan, pero no sustituyen asesoramiento jurídico cuando sea necesario.

## 9. Modelos y proveedores

### 9.1 Selección de modelos

Seleccionar entre modelos previamente autorizados según:

- Complejidad y tipo de tarea.
- Calidad y capacidad requeridas.
- Riesgo y sensibilidad de los datos.
- Privacidad y condiciones del proveedor.
- Disponibilidad.
- Coste y presupuesto restante.

### 9.2 Sustitución ante fallos

- Solo alternativas aprobadas previamente para el tipo de tarea.
- Comprobar capacidades, privacidad, coste y requisitos antes de cambiar.
- Verificar el resultado con criterios equivalentes.
- No cambiar de proveedor para eludir cuotas, controles o presupuesto.
- Si no hay alternativa adecuada, pausar la tarea y conservar el progreso verificable.

### 9.3 Verificación de capacidades

Cada capacidad crítica se clasifica como:

- **Confirmada:** comprobada con documentación y, cuando sea necesario, prueba real.
- **Pendiente:** aún no validada en el plan, cuenta o entorno objetivo.
- **No disponible:** no cumple el requisito.

No se debe depender de capacidades pendientes para un requisito crítico. Si una capacidad no existe, se documenta una alternativa compatible o se bloquea el requisito. Los límites de proveedor y garantías contractuales se documentan explícitamente.

## 10. Desarrollo de software y entrega

### 10.1 Flujo de trabajo

1. Entender requisitos y criterios de aceptación.
2. Diseñar y obtener aprobación antes de implementar cambios arquitectónicos.
3. Crear rama de trabajo.
4. Implementar con cambios acotados.
5. Ejecutar pruebas, lint, análisis de seguridad y verificaciones pertinentes.
6. Revisar el diff y los riesgos.
7. Abrir una solicitud de integración (PR) con resumen, evidencias, riesgos y verificaciones.
8. Esperar las protecciones y revisiones configuradas.
9. Integrar solo conforme a las reglas del repositorio.
10. Verificar el estado final y documentar el resultado.

### 10.2 Calidad

- Pruebas unitarias e integración según la tarea.
- Análisis estático, formato y seguridad según herramientas disponibles.
- Cobertura relevante sin usarla como único indicador de calidad.
- Pruebas de regresión proporcionales al impacto.
- Los controles críticos de seguridad y autorización no se omiten para conseguir una ejecución verde.
- Si una comprobación no puede ejecutarse, debe declararse como pendiente; nunca afirmarse que pasó.

### 10.3 Producción

- No hay despliegues autónomos en producción en la versión inicial.
- Publicación, despliegue o compromisos externos requieren autorización específica.
- Una PR o una CI correcta no constituye permiso para desplegar.
- Las protecciones de rama y los permisos de GitHub se mantienen; no se alteran sin aprobación explícita.

### 10.4 APIs y dependencias externas

- Detectar cambios y evaluar impacto.
- Probar compatibilidad en entorno aislado.
- Solo ajustes menores previamente autorizados y comprobados pueden aplicarse automáticamente.
- Cambios que afecten a seguridad, datos, costes, permisos o arquitectura requieren aprobación.
- Si un proveedor deja de cumplir un requisito crítico, pausar la función afectada.

## 11. Calidad de resultados y definición de terminado

Cada tarea tendrá criterios de aceptación explícitos cuando sea posible. La verificación será proporcional al riesgo:

- Código: pruebas, análisis y revisión del diff.
- Operaciones externas: consulta del estado real del recurso o transacción.
- Documentos: revisión de contenido, integridad y ubicación.
- Cambios sensibles: evidencia de autorización y comprobación posterior.
- Tareas de investigación: fuentes, vigencia y distinción entre hechos e inferencias.

Estados de tarea:

- **Completada:** criterios verificados y evidencia suficiente.
- **Parcial:** una parte se completó; se enumeran las partes pendientes.
- **Bloqueada:** requiere decisión, permiso, capacidad o condición externa.
- **Fallida:** no se logró el resultado previsto.
- **Pendiente de verificación:** existe indicio de ejecución, pero no evidencia suficiente.

Nunca se afirmará que algo está completado solo porque una herramienta devolvió una respuesta de éxito. La entrega no autoriza automáticamente publicación, integración o despliegue.

## 12. Recuperación, errores e incidentes

### 12.1 Recuperación limitada

- Reintentos acotados para errores transitorios seguros.
- Antes de repetir una operación, comprobar si ya se realizó.
- Aplicar correcciones solo dentro de permisos vigentes.
- Si el fallo persiste, aumenta el riesgo o no se puede verificar el estado, detener la parte afectada.
- No declarar éxito parcial como éxito completo.

### 12.2 Reanudación

- Leer el último checkpoint del proyecto.
- Verificar contra GitHub, Notion y el sistema de ejecución.
- Comprobar que permisos y autorizaciones siguen vigentes.
- Reanudar desde un punto seguro sin duplicar operaciones.
- Si el estado es incierto, bloquear la parte afectada y solicitar intervención.

### 12.3 Incidentes

- Contener: detener tareas afectadas, aislar herramientas o aplicar revocaciones previstas en los controles autorizados.
- Preservar evidencia segura y necesaria.
- Continuar tareas independientes solo si se confirma que no están afectadas.
- Notificar según prioridad.
- Revisar causa, impacto y medidas preventivas.
- Reanudar operaciones afectadas solo tras verificar integridad, permisos y autorización requerida.

### 12.4 Parada y revocación

La parada no dependerá únicamente de una instrucción conversacional. Se requieren controles independientes del modelo que bloqueen nuevas acciones y permitan revocar permisos en los sistemas correspondientes. Las operaciones en curso deben detenerse o completarse de forma segura según sus capacidades reales. Los mecanismos se probarán antes del piloto y periódicamente.

## 13. Costes y límites operativos

- Cada proyecto tendrá un presupuesto aprobado y límites de consumo.
- Advertir antes de alcanzar el límite cuando sea posible.
- Mantener un margen preventivo para costes pendientes o difíciles de medir.
- Cerca del límite, evitar nuevas tareas costosas y pausar preventivamente.
- Solo terminar operaciones en curso si detenerlas de inmediato generaría un riesgo y si la finalización está autorizada.
- No aumentar presupuesto ni cambiar de proveedor para evadir el límite.
- Si el proveedor no ofrece un límite duro, documentar la limitación y aplicar controles adicionales. No presentar alertas como si fueran bloqueos garantizados.
- Los presupuestos, cuotas, límites de concurrencia y umbrales se fijarán con datos reales del piloto.

## 14. Observabilidad, auditoría y comunicación

### 14.1 Registro de eventos

Registrar, según riesgo y finalidad:

- Identificador de tarea y proyecto.
- Objetivo y alcance.
- Herramientas utilizadas.
- Autorización aplicable y su vigencia, sin secretos.
- Acciones significativas y recursos afectados.
- Comprobaciones, evidencias, errores y resultado.
- Duración y consumo conocido.
- Decisiones, bloqueos y escalados.

### 14.2 Privacidad y retención

- No registrar credenciales, tokens o datos privados innecesarios.
- Restringir acceso a registros.
- Definir plazos por tipo de registro, finalidad y obligación aplicable.
- Eliminar datos cuando expire su finalidad o el plazo aplicable, salvo conservación justificada.
- Los registros no se convierten en memoria personal automáticamente.

### 14.3 Comunicación

- Resúmenes breves para trabajo rutinario.
- Explicaciones detalladas para decisiones importantes.
- Avisos oportunos de riesgos, bloqueos y fallos que requieran intervención.
- Prioridad y canal según reglas aprobadas.
- ChatGPT será el canal habitual; otros canales solo se usarán si están autorizados y su capacidad de entrega está verificada.
- No duplicar alertas innecesariamente.
- No afirmar que una notificación se entregó sin confirmación.
- El silencio del usuario nunca significa aprobación de una acción sensible.

## 15. Métricas y mejora continua

El cuadro de mando equilibrará:

- Calidad de resultados.
- Fiabilidad y tasa de recuperación segura.
- Eficiencia y duración.
- Autonomía dentro de permisos autorizados.
- Seguridad e incidentes.
- Costes y consumo.
- Aprendizaje validado y prevención de regresiones.

No se optimizará una métrica de forma aislada ni se compensará una regresión de seguridad con productividad. Los umbrales numéricos se fijarán tras obtener una línea base en el piloto.

La mejora continua seguirá este ciclo:

1. Detectar patrón o problema con evidencias.
2. Proponer cambio.
3. Evaluar impacto, riesgos y costes.
4. Probar en entorno aislado.
5. Revisar regresiones.
6. Aprobar según el impacto.
7. Versionar y aplicar.
8. Supervisar y revertir si se incumplen criterios.

No se afirmará que el modelo se reentrena automáticamente. Las mejoras pueden consistir en documentación, instrucciones aprobadas, código, pruebas o configuración versionada.

## 16. Continuidad y copias de seguridad

### 16.1 Elementos prioritarios

- Código y pruebas.
- Documentación oficial y decisiones aprobadas.
- Configuración recuperable.
- Checkpoints y estados esenciales de tareas.
- Registros críticos durante el periodo justificado.

### 16.2 Controles

- Combinar versionado nativo con copias adicionales de datos esenciales.
- Proteger copias frente a acceso y eliminación accidental.
- Excluir secretos y credenciales de copias ordinarias.
- Verificar integridad y realizar pruebas periódicas de restauración.
- Definir retención y eliminación.
- Establecer RPO y RTO por componente después de comprobar herramientas, costes y pruebas reales.

No se fijan cifras de RPO/RTO sin evidencia operativa. Las copias que no se han restaurado de prueba no se consideran recuperación validada.

## 17. Coordinación y conflictos entre módulos

- Un coordinador central asigna tareas y consolida resultados.
- Los módulos mantienen procedimientos, fuentes, herramientas y límites independientes.
- Los conflictos entre módulos se documentan.
- Los conflictos que afecten a seguridad, permisos, presupuesto, datos o decisiones de alto impacto se escalan.
- No se resuelven discrepancias escogiendo la respuesta más conveniente.
- Se comparte información entre módulos solo por una finalidad concreta y con autorización aplicable.

## 18. Requisitos no funcionales

- **Seguridad:** controles técnicos de autorización y revocación independientes del modelo.
- **Fiabilidad:** operaciones idempotentes cuando sea posible; verificación del estado antes de repetir acciones.
- **Auditabilidad:** reconstrucción de operaciones importantes sin exponer secretos.
- **Privacidad:** minimización, separación de proyectos, acceso restringido y retención limitada.
- **Mantenibilidad:** módulos independientes e interfaces claras para dependencias críticas.
- **Portabilidad selectiva:** documentar alternativas donde aporten valor; no crear abstracciones especulativas.
- **Coste:** límites explícitos y comportamiento definido al alcanzarlos.
- **Recuperación:** checkpoints, copias verificadas y reanudación segura.
- **Usabilidad:** comunicación adaptativa y resúmenes que eviten interrupciones innecesarias.

## 19. Dependencias críticas y verificación previa

Antes de implementar, crear una matriz de capacidades por proveedor e integración con:

- Capacidad requerida.
- Evidencia documental.
- Prueba en el entorno objetivo.
- Permisos requeridos.
- Coste y cuotas.
- Comportamiento ante fallo.
- Método de verificación del resultado.
- Posibilidad de parada y revocación.
- Alternativa aprobada, si existe.
- Estado: confirmada, pendiente o no disponible.

Debe verificarse especialmente:

1. Límites de gasto duros frente a simples alertas.
2. Revocación y alcance real de permisos.
3. Identificación fiable del estado de operaciones externas.
4. Capacidades de auditoría y retención.
5. Disponibilidad de entornos aislados.
6. APIs de GitHub, Notion y demás herramientas autorizadas.
7. Límites, disponibilidad y políticas de los modelos aprobados.
8. Capacidades de notificación y confirmación de entrega.
9. Copias y restauración.
10. Protección frente a acciones duplicadas.

Las capacidades pendientes no pueden sustentar requisitos críticos del lanzamiento.

## 20. Validación, piloto y lanzamiento

### 20.1 Fases

1. Revisión de requisitos y matriz de capacidades.
2. Pruebas automatizadas.
3. Pruebas de autorización, permisos y límites.
4. Simulación de fallos, estados inciertos y operaciones duplicadas.
5. Pruebas en entorno aislado.
6. Piloto supervisado en un proyecto acotado.
7. Revisión de métricas, incidentes, costes y riesgos residuales.
8. Aprobación explícita del lanzamiento para una versión y alcance concretos.

### 20.2 Bloqueadores de lanzamiento

- Cualquier acción no autorizada o bypass de permisos.
- Fallo crítico de seguridad no resuelto.
- Declaraciones falsas de finalización.
- Recuperación insegura o riesgo de duplicación no controlado.
- Ausencia de mecanismos de parada o revocación probados.
- Costes críticos sin límites o controles verificables.
- Capacidad crítica del proveedor sin validar.
- Imposibilidad de reconstruir una operación importante.
- Riesgos residuales críticos sin resolver.
- Falta de aprobación explícita del lanzamiento.

### 20.3 Criterios de aceptación de la arquitectura

La arquitectura podrá pasar a planificación de implementación cuando:

- Cada componente y fuente oficial tenga responsabilidad definida.
- Los permisos y decisiones reservadas estén documentados.
- Los requisitos críticos tengan pruebas observables.
- Los proveedores críticos estén verificados o claramente marcados como pendientes.
- Los fallos tengan una política segura de pausa y recuperación.
- La estrategia de registros, privacidad, costes y copias sea viable.
- Las tareas de implementación tengan alcance y dependencias claros.

La arquitectura no se considera lista para lanzamiento hasta superar las fases y bloqueadores anteriores.

## 21. Valores que deliberadamente quedan pendientes

No se inventan números para los siguientes parámetros; deben definirse con herramientas y datos reales:

- Presupuesto mensual y por proyecto.
- Umbrales de alerta y margen preventivo.
- Máximo de tareas simultáneas por herramienta.
- Cadencia exacta de revisión.
- Retención por categoría de registro.
- RPO/RTO por componente.
- Límites de reintentos por tipo de operación.
- Umbrales de métricas y criterios cuantitativos del piloto.
- Modelos y proveedores concretos aprobados.
- Duración del piloto.
- Canales de notificación y excepciones horarias.

Cada parámetro deberá incluir valor, unidad, propietario, justificación, fuente de evidencia, procedimiento de cambio y comportamiento ante su incumplimiento.

## 22. Registro de decisiones arquitectónicas aprobadas

| ID      | Decisión                                                                                            |
| ------- | --------------------------------------------------------------------------------------------------- |
| ADR-001 | Arquitectura híbrida; ChatGPT como interfaz principal.                                              |
| ADR-002 | Memoria curada; los cambios permanentes importantes requieren confirmación.                         |
| ADR-003 | Autonomía con límites estrictos; Santi no se concede permisos a sí mismo.                           |
| ADR-004 | Catálogo de herramientas y modelos autorizado; no se crean conexiones automáticamente.              |
| ADR-005 | Calidad por capas y proporcional al riesgo; no se debilitan controles críticos.                     |
| ADR-006 | Recuperación limitada; comprobar el estado antes de repetir operaciones.                            |
| ADR-007 | Seguridad por capas, mínimo privilegio, separación de credenciales y revocación.                    |
| ADR-008 | Comunicación adaptativa y alertas por prioridad.                                                    |
| ADR-009 | Núcleo común y módulos independientes por especialidad.                                             |
| ADR-010 | Planificación por objetivos con cambios de alcance sujetos a aprobación.                            |
| ADR-011 | Entregas mediante ramas, pruebas, PR y protecciones; sin despliegues autónomos en producción en v1. |
| ADR-012 | Métricas equilibradas; seguridad no compensable por productividad.                                  |
| ADR-013 | Registros estructurados proporcionales al riesgo; no son memoria por defecto.                       |
| ADR-014 | Continuidad segura, preservación de progreso y verificación antes de reanudar.                      |
| ADR-015 | V1 funcional controlada, centrada en desarrollo de software.                                        |
| ADR-016 | Validación por etapas y lanzamiento con aprobación explícita.                                       |
| ADR-017 | Control de cambios por impacto, versionado y reversión cuando sea posible.                          |
| ADR-018 | Documentación distribuida por función y una fuente oficial por tipo de dato.                        |
| ADR-019 | Presupuesto por proyecto, límites estrictos y pausa al alcanzar el límite.                          |
| ADR-020 | Portabilidad selectiva, solo donde aporte valor demostrado.                                         |
| ADR-021 | Verificación de fuentes proporcional al riesgo.                                                     |
| ADR-022 | Separación de datos por proyecto y especialidad.                                                    |
| ADR-023 | Checkpoints estructurados y estado canónico en Notion/GitHub, validado frente al sistema real.      |
| ADR-024 | Control de cambios de alcance según impacto.                                                        |
| ADR-025 | Coordinador central con especialistas independientes.                                               |
| ADR-026 | Selección de modelos según tarea, riesgo, privacidad y coste; alternativas preaprobadas.            |
| ADR-027 | Gestión de incertidumbre proporcional al riesgo.                                                    |
| ADR-028 | Paralelismo limitado y coordinado.                                                                  |
| ADR-029 | Sin panel personalizado en v1; ChatGPT y vistas nativas.                                            |
| ADR-030 | Alertas por prioridad, canal autorizado y capacidad de entrega verificada.                          |
| ADR-031 | Contención selectiva ante emergencias sin ampliar permisos.                                         |
| ADR-032 | Copias por criticidad y restauración probada.                                                       |
| ADR-033 | RPO/RTO diferenciados y fijados con evidencia.                                                      |
| ADR-034 | Definición de terminado basada en criterios y evidencia.                                            |
| ADR-035 | Mejora basada en evidencia, pruebas, aprobación y versionado.                                       |
| ADR-036 | Pruebas de regresión y bloqueo ante fallos críticos.                                                |
| ADR-037 | Decisiones técnicas delegadas dentro del alcance; decisiones de alto impacto reservadas al usuario. |
| ADR-038 | Límites éticos evaluados por contexto; rechazo de acciones dañinas y alternativas seguras.          |
| ADR-039 | Jerarquía explícita de instrucciones; fuentes externas como datos sin autoridad.                    |
| ADR-040 | Matriz de coherencia y resolución explícita de dependencias arquitectónicas.                        |
| ADR-041 | Sin panel personalizado en v1; reconsiderar solo si el piloto demuestra necesidad.                  |
| ADR-042 | Pausa preventiva y margen de seguridad ante límites de coste.                                       |
| ADR-043 | Controles técnicos independientes de las instrucciones del modelo.                                  |
| ADR-044 | Fuente de verdad definida por tipo de dato.                                                         |
| ADR-045 | Criterios de lanzamiento observables y proporcionales al riesgo.                                    |
| ADR-046 | Verificar capacidades de proveedores antes de depender de ellas.                                    |
| ADR-047 | Autorizaciones limitadas, vigentes, verificables y revocables.                                      |
| ADR-048 | Notificaciones por prioridad y canales autorizados.                                                 |
| ADR-049 | Retención de registros según finalidad y riesgo.                                                    |
| ADR-050 | Paralelismo limitado por herramienta y proyecto.                                                    |
| ADR-051 | Sustitución solo entre modelos autorizados y validados.                                             |
| ADR-052 | Revisiones periódicas y extraordinarias tras incidentes.                                            |
| ADR-053 | Acceso a datos según sensibilidad y finalidad.                                                      |
| ADR-054 | Recuperación tras verificar el estado real.                                                         |
| ADR-055 | Copias de seguridad por criticidad y capacidad de recuperación.                                     |
| ADR-056 | RPO/RTO fijados tras validar herramientas y costes.                                                 |
| ADR-057 | Reserva de seguridad y pausa preventiva ante límites económicos.                                    |
| ADR-058 | Contención urgente reversible y previamente autorizada.                                             |
| ADR-059 | Verificación proporcional al riesgo y a los criterios de aceptación.                                |
| ADR-060 | Resolución de conflictos por fuente oficial y bloqueo ante discrepancias críticas.                  |
| ADR-061 | Contenido externo tratado como entrada no confiable.                                                |
| ADR-062 | Aprobación humana explícita del lanzamiento.                                                        |
| ADR-063 | Piloto supervisado en un proyecto real acotado.                                                     |
| ADR-064 | Parada y revocación mediante controles independientes del modelo.                                   |
| ADR-065 | Supervisión humana proporcional al impacto.                                                         |
| ADR-066 | Cambios de APIs y proveedores validados antes de adoptarse.                                         |
| ADR-067 | Conocimiento técnico actualizado mediante verificación, versionado y aprobación según impacto.      |
| ADR-068 | Licencias de dependencias verificadas según riesgo.                                                 |
| ADR-069 | Vulnerabilidades gestionadas según severidad, exposición y compatibilidad.                          |
| ADR-070 | Separación de entornos y acceso mínimo a producción y datos reales.                                 |

## 23. Relación con el repositorio actual

El repositorio `danihal1224/santi-dev-workflow` es actualmente un starter de Python con controles de ingeniería; no constituye por sí solo la aplicación Santi ni implementa esta arquitectura. Esta especificación documenta el diseño sin renombrar el paquete de ejemplo ni alterar código, configuración, CI o protecciones de rama.

Las instrucciones de ingeniería existentes siguen vigentes. La implementación futura debe dividirse en tareas pequeñas, cada una con pruebas y criterios de aceptación, y desarrollarse en ramas con PR. No se deben mezclar cambios de documentación arquitectónica con la implementación del sistema sin una decisión explícita.

## 24. Próximo paso propuesto

Después de revisar y aprobar esta especificación:

1. Preparar un plan de implementación por fases.
2. Verificar la matriz de capacidades de herramientas y proveedores.
3. Identificar un entorno de piloto aislado y el repositorio de prueba.
4. Definir valores operativos con evidencias: presupuesto, concurrencia, retención, RPO/RTO y umbrales.
5. Implementar primero el control de autorizaciones, la trazabilidad, la verificación y la parada segura antes de ampliar autonomía.
6. Validar el piloto y obtener aprobación explícita antes del lanzamiento.

**Estado final de este documento:** diseño técnico redactado para revisión. No se ha implementado la arquitectura, no se han habilitado nuevos permisos y no se autoriza el lanzamiento.
