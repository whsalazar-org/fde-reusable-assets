# Hoja de ruta de adopción de Copilot y SDLC agéntico

Esta hoja de ruta proporciona un camino práctico desde un piloto enfocado de Copilot hasta un despliegue empresarial. Combina un modelo de acompañamiento técnico con patrones de adopción recomendados por GitHub y pone énfasis en resultados medibles.

> **Duración sugerida:** planifique un piloto de dos a cuatro semanas, con captura frecuente de evidencias, una transferencia documentada y un plan de continuidad.

## Resultado esperado

Al finalizar la participación, el cliente debe contar con:

1. Un equipo piloto que use Copilot en una parte acordada del SDLC agéntico.
2. Estándares de gobernanza para `copilot-instructions.md`, instrucciones personalizadas y archivos de prompts, skills, agentes personalizados y servidores MCP.
3. Un proyecto prioritario con resultados documentados antes y después.
4. Métricas observables que midan resultados de ingeniería, calidad y durabilidad de la adopción.
5. Artefactos reutilizables publicados en el portal interno del cliente Awesome Copilot.
6. Una hoja de ruta práctica de expansión para equipos adicionales.

## Modelo de entrega recomendado

Comenzar con **un equipo piloto y un proyecto prioritario** en lugar de intentar un despliegue empresarial completo.

El piloto debe contar con:

- Un referente de ingeniería designado.
- Un gerente de ingeniería o patrocinador ejecutivo.
- Un código activo con un objetivo de entrega significativo.
- Flujos existentes de control de código fuente, pull requests, CI e incidencias.
- Acceso suficiente para configurar el repositorio y recopilar métricas.
- Dos o tres ingenieros que puedan colaborar con el equipo facilitador.
- Un resultado empresarial o de ingeniería claro que se pueda medir.

El equipo facilitador debe colaborar con los ingenieros de la organización en lugar de construir la solución de forma independiente. La organización debe ser responsable de los estándares, las configuraciones y el modelo operativo resultantes.

## Fase 1: Establecimiento de la línea base y definición del piloto

**Periodo:** Días 1–2

### Actividades

- Confirmar el equipo piloto, el proyecto prioritario y el objetivo empresarial.
- Documentar el flujo actual de entrega de software: requisitos, planificación, implementación, pruebas, revisión de código, despliegue y mantenimiento.
- Identificar dónde utiliza Copilot actualmente el equipo.
- Registrar las métricas de línea base del proyecto prioritario.
- Identificar las restricciones de seguridad, cumplimiento, gestión de datos y red.
- Definir los escenarios del SDLC agéntico que se probarán.

### Escenarios de piloto recomendados

Seleccionar entre tres y cinco escenarios, como los siguientes:

1. Convertir una incidencia en un plan de implementación.
2. Usar un agente personalizado para investigar un área de código desconocida.
3. Usar Copilot para implementar un cambio acotado a partir de una incidencia.
4. Generar o mejorar pruebas para un cambio.
5. Usar Copilot Code Review para identificar defectos o cobertura faltante.
6. Usar un servidor MCP para recuperar contexto interno de ingeniería aprobado.
7. Generar notas de versión o documentación operativa a partir del trabajo completado.

### Entregables y criterios de salida

- Acta del piloto y mapa del flujo de trabajo actual.
- Métricas de línea base y registro inicial de riesgos.
- Escenarios de piloto seleccionados y definición del éxito.
- Acuerdo sobre el alcance, los repositorios, los entornos y el método de medición.

## Fase 2: Diseño de la gobernanza y los estándares

**Periodo:** Días 2–5

Crear un modelo ligero de gobernanza antes de producir un gran número de activos reutilizables.

| Área | Estándar que se debe definir |
|---|---|
| `copilot-instructions.md` | Alcance, responsables, herencia, frecuencia de revisión y contenido requerido |
| Instrucciones personalizadas | Convención de nombres, ubicación, audiencia prevista y proceso de validación |
| Archivos de prompts | Cuándo usar prompts frente a skills, entradas requeridas y resultados esperados |
| Skills | Propósito, patrón de invocación, dependencias, responsable de mantenimiento y validación |
| Agentes personalizados | Responsabilidades, herramientas, permisos y límites de escalamiento |
| Servidores MCP | Fuentes de datos aprobadas, autenticación, controles de red, registro y retención |
| Seguridad | Gestión de secretos, clasificación de datos, exclusiones de contenido y puntos de aprobación humana |
| Calidad | Pruebas requeridas, expectativas de revisión y evidencia de que los cambios generados son seguros |
| Ciclo de vida | Versionado, responsables, revisión, obsolescencia y retiro |

### Principios de gobernanza

- Mantener las instrucciones específicas para el repositorio, el equipo o el flujo de trabajo al que sirven.
- Preferir activos pequeños y componibles en lugar de un único archivo de instrucciones grande.
- Exigir revisión humana para código, configuración, cambios sensibles a la seguridad y acciones en producción.
- No incluir nunca secretos, tokens, cadenas de conexión ni datos que identifiquen al cliente en instrucciones, prompts, skills o configuración de MCP.
- Limitar los servidores MCP a herramientas y fuentes de datos aprobadas; usar acceso de solo lectura de forma predeterminada.
- Exigir aprobación explícita antes de que los agentes modifiquen sistemas de producción o realicen cambios irreversibles.
- Almacenar los activos reutilizables en el control de código fuente con un responsable y un proceso de revisión.
- Probar los activos reutilizables con tareas representativas antes de publicarlos ampliamente.

### Entregables

- Estándar de gobernanza de Copilot.
- Estándar de nombres y estructura de repositorios.
- Lista de comprobación de seguridad y aprobación de MCP.
- Flujo de revisión y publicación.
- Matriz de responsables para cada tipo de artefacto.

## Fase 3: Construcción del piloto de SDLC agéntico

**Periodo:** Días 5–10

Implementar los estándares en el proyecto prioritario y probarlos con trabajo de ingeniería real.

### Secuencia de implementación recomendada

#### 1. Instrucciones del repositorio

Crear instrucciones a nivel de proyecto que cubran la arquitectura, las dependencias, los comandos de compilación y pruebas, las convenciones de código, los requisitos de seguridad, las directrices específicas por directorio y la definición de terminado.

#### 2. Instrucciones y prompts personalizados

Crear flujos reutilizables para el refinamiento de incidencias, la planificación técnica, la exploración del código, la planificación de pruebas, la preparación de pull requests, el seguimiento de revisiones de código y las actualizaciones de documentación.

#### 3. Skills

Cada skill debe definir su propósito, entradas, resultados esperados, herramientas o sistemas utilizados, límites de seguridad, ejemplo de invocación, método de validación y responsable.

#### 4. Agentes personalizados

Crear agentes únicamente cuando un rol diferenciado mejore el flujo de trabajo. Entre los posibles roles se incluyen planificación, análisis de pruebas, documentación, preparación de revisiones de código y modernización del repositorio. Mantener los agentes acotados, con permisos mínimos y límites de escalamiento claros.

#### 5. Servidores MCP

Introducir un servidor MCP únicamente cuando proporcione información o acciones aprobadas a las que Copilot no pueda acceder mediante el contexto existente del repositorio.

Para cada servidor MCP, documentar sus datos, autenticación, permisos, operaciones de lectura y escritura, registro de auditoría, retención, comportamiento ante fallos y responsable de seguridad.

### Entregables

- Proyecto prioritario configurado.
- Biblioteca inicial de instrucciones y prompts.
- Una o más skills piloto.
- Uno o más agentes personalizados con alcance limitado.
- Integración MCP aprobada, si está justificada.
- Ejemplos antes y después de trabajo real.
- Guía operativa del piloto.

## Fase 4: Medición de resultados e iteración

**Periodo:** Días 8–15

Medir si Copilot mejoró los resultados de ingeniería. No considerar los inicios de sesión, los prompts ni las líneas de código generadas como métricas principales de éxito.

### Métricas de resultados recomendadas

| Área de resultados | Métrica de ejemplo |
|---|---|
| Velocidad de entrega | Tiempo desde la aceptación de la incidencia hasta el pull request fusionado |
| Eficiencia del flujo | Tiempo de espera frente al tiempo dedicado a la ingeniería |
| Calidad | Tasa de defectos escapados, tasa de retrabajo o tasa de errores posteriores a la fusión |
| Efectividad de la revisión | Tiempo del ciclo de revisión e iteraciones sustantivas de revisión |
| Confianza en las pruebas | Cobertura de las rutas modificadas y defectos de pruebas escapados |
| Experiencia del desarrollador | Tiempo necesario para comprender un área de código desconocida |
| Mantenimiento | Tiempo para resolver defectos recurrentes o actualizar dependencias |
| Autosuficiencia | Porcentaje de tareas del piloto completadas sin apoyo del equipo facilitador |
| Reutilización | Número de equipos que usan correctamente un activo publicado |
| Durabilidad | Rendimiento de los resultados después de que finaliza el acompañamiento |

### Método de medición

1. Capturar una línea base antes de cambiar el flujo de trabajo.
2. Seleccionar elementos de trabajo comparables para el piloto.
3. Registrar el tipo y la complejidad de la tarea, los miembros del equipo y las restricciones.
4. Comparar los resultados con la línea base o con un conjunto de control similar.
5. Combinar los datos cuantitativos con comentarios de los ingenieros, hallazgos de revisión, defectos encontrados y ejemplos de reducción del retrabajo.
6. Registrar las limitaciones y los factores de confusión.

### No usar como métricas principales de éxito

- Número de prompts enviados.
- Número de sugerencias de Copilot aceptadas.
- Líneas de código generadas.
- Número de agentes o skills creados.
- Número de repositorios configurados.
- Número de usuarios habilitados sin evidencia de mejores resultados.

Estos pueden ser indicadores complementarios, pero no demuestran valor empresarial o de ingeniería.

## Fase 5: Publicación del portal interno Awesome Copilot

**Periodo:** Días 12–20

El portal debe ser el catálogo interno del cliente para prácticas reutilizables de Copilot, no una colección de ejemplos no verificados.

### Estructura recomendada

```text
Awesome Copilot
├── Primeros pasos
├── Gobernanza
├── Instrucciones de repositorio
├── Instrucciones personalizadas
├── Archivos de prompts
├── Skills
├── Agentes personalizados
├── Servidores MCP
├── Patrones de SDLC agéntico
├── Casos de uso por rol
├── Casos de uso por tecnología
├── Seguridad y cumplimiento
├── Métricas y evaluación
├── Casos de estudio
└── Guía de contribución
```

### Metadatos requeridos para cada activo

- Nombre y propósito.
- Audiencia prevista.
- Repositorios o equipos aplicables.
- Responsable y versión.
- Dependencias y permisos requeridos.
- Consideraciones de seguridad.
- Ejemplo de uso y resultado esperado.
- Estado de validación y limitaciones conocidas.
- Fecha de la última revisión.
- Canal de comentarios o soporte.

### Flujo de publicación

1. El equipo piloto crea y prueba el activo.
2. Un responsable de ingeniería revisa la implementación.
3. Los responsables de seguridad o plataforma revisan los permisos y la gestión de datos cuando sea necesario.
4. Se asignan una versión y un responsable al activo.
5. El activo se publica con un ejemplo y los resultados de validación.
6. Un segundo equipo prueba el activo antes de marcarlo como listo para la empresa.
7. Se recopilan comentarios sobre el uso y los resultados.
8. Los activos se revisan periódicamente y se retiran cuando dejan de aportar valor.

### Criterios de éxito del portal

- Un segundo equipo puede encontrar y usar los activos del piloto sin asistencia del equipo facilitador.
- Los activos publicados incluyen responsables, instrucciones de uso y límites de seguridad.
- El contenido experimental, aprobado por el equipo y listo para la empresa se distingue claramente.
- El cliente cuenta con un proceso de contribución y revisión.
- Al menos un patrón del piloto se reutiliza correctamente fuera del equipo original.

## Fase 6: Cierre, transferencia y expansión

**Periodo:** Últimos tres a cinco días

### Actividades de cierre

- Repetir la evaluación de capacidades de línea base.
- Comparar los resultados con los criterios de éxito acordados.
- Documentar qué escenarios del SDLC agéntico produjeron valor medible.
- Documentar los experimentos fallidos o de bajo valor.
- Finalizar los estándares de gobernanza.
- Publicar los artefactos validados en el portal.
- Identificar los riesgos y las dependencias restantes.
- Preparar la transferencia al equipo responsable de la continuidad.
- Definir los dos o tres equipos siguientes para la expansión.

El informe final de la participación debe incluir resultados antes y después, trabajo completado, artefactos reutilizables, comentarios sobre el producto, señales de ROI y el plan de continuidad.

### Criterios de expansión

Expandir únicamente cuando el piloto demuestre:

- Al menos una mejora medible en velocidad de entrega, calidad o efectividad del desarrollador.
- Ningún problema crítico de seguridad o gobernanza sin resolver.
- Un modelo operativo bajo responsabilidad del cliente.
- Un artefacto reutilizable que otro equipo pueda aplicar.
- Un responsable designado para el soporte continuo.
- Un plan de medición para el equipo siguiente.

## Ejemplo de plan de piloto de 30 días

| Periodo | Enfoque | Resultados clave |
|---|---|---|
| Días 1–2 | Línea base y definición del piloto | Acta, mapa del flujo de trabajo y métricas |
| Días 3–5 | Diseño de la gobernanza | Estándares, modelo de responsabilidades y controles de MCP |
| Días 6–10 | Implementación inicial | Instrucciones, prompts, skills y agentes |
| Días 11–15 | Ejecución de tareas reales | Evidencia del piloto y mejoras del flujo de trabajo |
| Días 16–20 | Medición y publicación en el portal | Resultados, activos validados y contenido del portal |
| Días 21–25 | Validación con un segundo equipo | Prueba de reutilización, comentarios y actualizaciones de gobernanza |
| Días 26–30 | Cierre y planificación de la expansión | Informe final, transferencia y hoja de ruta de escalado |

## Definición de terminado

La participación finaliza cuando:

- El equipo piloto ha completado trabajo de ingeniería representativo usando los patrones de SDLC agéntico acordados.
- Los resultados se han comparado con una línea base documentada.
- El cliente puede explicar dónde Copilot mejoró los resultados y dónde no lo hizo.
- Los estándares de gobernanza cubren instrucciones, prompts, skills, agentes y servidores MCP.
- Los activos reutilizables se han publicado con responsables y estado de validación.
- Un segundo equipo ha probado al menos un patrón reutilizable.
- El equipo responsable de la continuidad y el liderazgo de ingeniería cuentan con un plan de continuidad y expansión.
- El informe final de la participación y los materiales de transferencia están completos.
