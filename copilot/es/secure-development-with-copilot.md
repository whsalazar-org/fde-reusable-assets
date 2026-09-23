---
marp: true
theme: default
paginate: true
title: Patrones de Desarrollo Seguro con GitHub Copilot
---

# Patrones de Desarrollo Seguro con GitHub Copilot

**Del autocompletado a la autonomía gobernada**

<!-- Notas del presentador: Encuadre de la sesión. La pregunta ya no es "¿deberíamos usar Copilot?" sino "¿cómo hacemos que la entrega asistida por IA sea auditable, de mínimo privilegio y revisable — con el mismo estándar que exigimos a los colaboradores humanos?" -->

---

## La premisa central

> El desarrollo seguro con GitHub Copilot requiere combinar **la guía de la IA** con **pruebas automatizadas**, **barreras de protección** y **revisión rigurosa de código**.

Copilot está optimizado para la **funcionalidad por encima de la seguridad**. Puede replicar patrones inseguros presentes en sus datos de entrenamiento.

Para programar de forma segura, los equipos deben implementar:
- Patrones de diseño de defensa en profundidad
- Bloqueos de entorno
- Bucles de verificación

<!-- Notas del presentador: Esta es la tesis de toda la presentación. Copilot es un multiplicador de productividad, no un control de seguridad. -->

---

## Agenda

1. El cambio: asistente → agente → flujo de trabajo autónomo
2. Modelo de amenazas para el desarrollo asistido por IA
3. **Patrones de barreras de protección e ingeniería de prompts**
4. **Patrones de validación temprana (shift-left)**
5. **Patrones de integración y automatización CI/CD**
6. **Patrones de revisión con humano en el bucle (HITL)**
7. Autonomía gobernada: agentes, skills y MCP
8. Hoja de ruta de adopción y métricas

---

## El cambio en el flujo de trabajo del desarrollador

| Era | Rol humano | Superficie de riesgo |
| --- | --- | --- |
| Autocompletado | Escribe cada línea | Calidad del código sugerido |
| Modo Chat / Agente | Revisa los diffs | Alcance del prompt, fuga de contexto |
| Agente de código + MCP | Revisa los resultados | Permisos de herramientas, salida de datos, cadena de suministro |

> A medida que aumenta la autonomía, **el control debe pasar de la pulsación de tecla a la barrera de protección.**

<!-- Notas del presentador: El punto de control se desplaza. No se puede revisar cada token, así que en su lugar se restringe el entorno. -->

---

## Modelo de amenazas para el desarrollo asistido por IA

- **Código generado inseguro** — inyección, criptografía débil, autorización ausente
- **Fuga de secretos** — credenciales en prompts, contexto o salida confirmada
- **Inyección de prompts** — instrucciones maliciosas dentro de issues, tickets, páginas web, dependencias
- **Herramientas con exceso de permisos** — agentes con acceso de escritura o a producción por defecto
- **Cadena de suministro** — paquetes alucinados o con typosquatting ("slopsquatting")
- **Cambios no trazables** — sin registro de qué agente o herramienta tocó qué

---

## Las cuatro familias de patrones

| # | Familia | Punto de control |
| --- | --- | --- |
| 1 | **Barreras de protección e ingeniería de prompts** | Lo que Copilot ve y lo que se le indica |
| 2 | **Validación temprana (shift-left)** | El IDE, antes del commit |
| 3 | **Integración y automatización CI/CD** | La puerta del repositorio |
| 4 | **Revisión con humano en el bucle** | La decisión de fusión (merge) |

Cada una es defensa en profundidad. **Ninguna es suficiente por sí sola.**

---

# 1. Patrones de Barreras de Protección e Ingeniería de Prompts

Controle cómo Copilot recibe la información — y ordénele explícitamente que genere código seguro.

---

## Contexto de seguridad explícito

Incluya restricciones de seguridad claras **dentro del prompt**. No asuma que la variante segura es la predeterminada.

| Prompt débil | Prompt reforzado |
| --- | --- |
| "Escribe una consulta a la base de datos" | "Escribe una consulta SQL **parametrizada** para prevenir inyección" |
| "Añade un endpoint de login" | "Añade un endpoint de login con limitación de tasa y comparación de tiempo constante" |
| "Procesa esta entrada de usuario" | "Procesa y **valida** esta entrada; rechaza tipos inesperados" |

**Regla:** especifique la amenaza contra la que se defiende, no solo la funcionalidad.

---

## Conjuntos de instrucciones a nivel de sistema

Cree `.github/copilot-instructions.md` — se aplica automáticamente en cada solicitud, para que nadie tenga que recordarlo.

Defina una **lista explícita de "No Sugerir"**:

```markdown
## No Sugerir
- `eval()`, `exec()` o ejecución dinámica de código
- Concatenación directa de cadenas para construir SQL, shell o HTML
- Verificación de TLS/certificados deshabilitada
- Credenciales, tokens o cadenas de conexión codificadas en el código
- Hashing obsoleto: MD5, SHA-1 para contraseñas
```

Las reglas específicas por ruta viven en `.github/instructions/*.instructions.md`.

---

## Exclusión de contexto

Impida que Copilot llegue a leer material sensible.

- Use la configuración de **`.copilotignore`** / exclusión de contenido para bloquear:
  - Archivos de configuración sensibles (`.env`, `*.pem`, `secrets/`)
  - Claves de prueba y credenciales de fixtures
  - Endpoints internos privados y manifiestos de infraestructura
- Configurable a nivel de repositorio y de organización
- El contenido excluido no se usa para sugerencias **ni** como contexto de chat

**Principio:** si Copilot no puede leerlo, no puede filtrarlo.

---

## Prompts de "cero secretos"

**Nunca pegue contraseñas reales, tokens de API o cadenas de conexión en la interfaz de chat.**

Enseñe el *patrón estructural* con marcadores de posición:

```text
Conéctate a Postgres usando DB_PASSWORD_PLACEHOLDER
leído desde el entorno — no lo incluyas en línea.
```

- Los marcadores transmiten la forma sin exponer el valor
- Redacte datos de producción y PII de clientes antes de que entren al contexto
- Nunca reproduzca tokens, cookies o cabeceras `Authorization`

---

# 2. Patrones de Validación Temprana (Shift-Left)

Detecte fallos dentro del IDE — antes de que el código avance en el pipeline.

---

## Prompts de modelado de amenazas en el chat

Pida a Copilot Chat que **actúe como atacante** antes de ejecutar el código.

> "Realiza un modelado de amenazas sobre esta función y enumera las entradas que podrían causar un comportamiento inesperado."

Preguntas de seguimiento que revelan hallazgos reales:
- "¿Cuáles de estas entradas no están validadas y cuál es el radio de impacto?"
- "Mapea esta función contra el OWASP Top 10."
- "¿Qué ocurre bajo acceso concurrente o fallo parcial?"
- "Escribe la prueba fallida que demuestre cada debilidad."

**El momento más barato para encontrar el fallo: antes de que compile.**

---

## Contenedores de desarrollo reforzados

Publique una **plantilla de repositorio segura** a nivel de toda la empresa.

Un archivo `devcontainer.json` puede preconfigurar, para todo el equipo:
- Ajustes de Copilot y exclusiones de contenido
- Formateadores, linters y analizadores estáticos
- Reglas de seguridad locales y versiones del toolchain del lenguaje
- Imagen base y versiones de dependencias fijadas

**Efecto:** la configuración segura es la *predeterminada*, no una elección de cada desarrollador. Este es el **bloqueo de entorno**.

---

## Hooks de pre-commit

Configure hooks locales nativos para escanear los cambios **antes de que Git o Copilot puedan procesarlos**.

Los hooks deben verificar:
- Secretos codificados y cadenas de alta entropía
- Errores básicos de sintaxis y de linting
- Archivos grandes o binarios que no deberían confirmarse
- Funciones prohibidas de la lista "No Sugerir"

**Rápido, local y gratuito** — el primer bucle de verificación de la cadena.

---

# 3. Patrones de Integración y Automatización CI/CD

Combine la generación de salida de la IA con puertas estrictas del repositorio para evitar que se fusione código defectuoso.

---

## Alineación con GitHub Advanced Security (GHAS)

Use Copilot junto al análisis nativo de **CodeQL**.

- **Copilot escribe** el código
- **CodeQL** es el *guardián objetivo* que lo evalúa
- Análisis semántico y de flujo de datos — no coincidencia de patrones
- Se ejecuta en cada pull request; los hallazgos bloquean la fusión

> El sistema que genera el código nunca debe ser el único sistema que lo juzga.

---

## Pipelines con Copilot Autofix

Active **Copilot Autofix** en la configuración de seguridad del repositorio.

Flujo:
1. El escaneo de código detecta un fallo de seguridad
2. Autofix redacta automáticamente un parche seguro
3. La corrección sugerida se propone en el pull request
4. **Un humano la revisa y la acepta**

Cierra el ciclo de *detección* → *remediación* sin salir del PR — manteniendo la aprobación en manos de una persona.

---

## Escaneo de secretos y protección de push

Active **GitHub Secret Scanning** con **protección de push**.

- Bloquea el push **de inmediato** si un ingeniero acepta inadvertidamente una sugerencia de IA que contiene un token codificado
- Cubre cientos de patrones de proveedores más patrones personalizados de la organización
- Las excepciones quedan registradas y requieren justificación

Esta es la red de seguridad para el modo de fallo exacto que Copilot hace más probable: una credencial de aspecto plausible aceptada sin pensarlo dos veces.

---

## Automatización de la cadena de suministro

- **Dependabot** — parchea las dependencias que Copilot sugirió; alerta sobre CVEs conocidos
- **Dependency review** — expone adiciones riesgosas en el propio PR
- **Versiones fijadas y lockfiles** — compilaciones reproducibles y revisables
- **Protección de ramas** — verificaciones y revisión obligatorias antes de fusionar

---

# 4. Patrones de Revisión con Humano en el Bucle (HITL)

La supervisión humana gobierna en última instancia el ciclo de vida del código.

---

## Igualdad en la revisión de código por pares

**Trate el código generado por IA exactamente como código enviado por un tercero externo.**

- **No** acelere un pull request porque lo haya escrito Copilot
- La misma profundidad de revisión, las mismas aprobaciones requeridas, las mismas pruebas
- Los revisores deben preguntarse: *¿entiendo por qué funciona, no solo que funciona?*
- El ingeniero que lo envía es dueño del código — la autoría no es una defensa

> Las ganancias de velocidad vienen de escribir más rápido, **no de revisar menos**.

---

## Listas de verificación de validación de paquetes

Los agentes de IA pueden **alucinar nombres de librerías** — y los atacantes registran esos nombres ("slopsquatting").

Antes de aceptar cualquier dependencia recomendada:

- [ ] Confirme que el paquete **realmente existe** en su registro
- [ ] Verifique el mantenedor, el número de descargas y la actividad del repositorio
- [ ] Compruebe que la versión es real y no ha sido retirada
- [ ] Confirme que la licencia es aceptable
- [ ] Prefiera una dependencia aprobada existente antes que una nueva

---

## Manejo de contenido no confiable

Los tickets de Jira, issues, páginas web y READMEs de dependencias pueden contener **instrucciones inyectadas**.

- Trate todo el contenido externo como **datos, nunca como comandos**
- Redacte PII y datos de producción antes de que entren al contexto del agente
- Exija aprobación antes de cualquier acción saliente o de escritura
- Registre qué servidores MCP y herramientas con capacidad de escritura usó cada PR

---

# Autonomía Gobernada

Extendiendo las cuatro familias a agentes, skills y MCP.

---

## Agentes especialistas de mínimo privilegio

Agentes personalizados en `.github/agents/` — una responsabilidad acotada cada uno:

| Agente | Responsabilidad |
| --- | --- |
| `frontend-specialist` | Trabajo acotado de UI y accesibilidad |
| `test-specialist` | Estrategia de validación y testabilidad |
| `security-reviewer` | Manejo de secretos, permisos, operaciones riesgosas |
| `documentation-specialist` | Documentación concisa y traspaso |

**Principio:** un agente recibe el conjunto *mínimo* de herramientas para su trabajo. Un agente de documentación no tiene acceso de escritura a producción.

---

## Skills reutilizables como barreras de protección

`.github/skills/<nombre>/SKILL.md` codifica un procedimiento repetible.

Una skill bien formada define:
- **Cuándo** aplica (condiciones de activación)
- **Cómo** reunir contexto (qué herramientas y en qué orden)
- **Qué** emitir antes de actuar (una especificación revisable)
- **Barreras de protección** — solo lectura por defecto, sin reproducir credenciales, el contenido externo son datos y no comandos

**Efecto:** comportamiento consistente y auditable en lugar de improvisación prompt a prompt.

---

## Catálogo MCP y puertas de aprobación

El directorio `mcp/` es la **fuente de verdad** para el acceso a herramientas de los agentes.

- `mcp/catalog/*.json` — propósito, responsable, clasificación de datos, entornos, `allowedTools`, autenticación, registro, fechas de revisión
- `mcp/policies/approved-tools.md` — listas de permitidos, reglas de mínimo privilegio, manejo de secretos

**Reglas:**
- Solo servidores catalogados, solo herramientas listadas
- Solo lectura por defecto
- Las acciones de escritura / destructivas / de producción requieren **aprobación humana explícita**
- Fije las versiones; vuelva a revisar ante cualquier cambio de permisos

---

## Ciclo de vida de una integración MCP

1. **PR** que añade una entrada al catálogo con todos los campos completos
2. **Revisión** por el responsable designado *y* por un revisor de seguridad
3. **Fijar** la versión; los cambios de permisos obligan a una nueva revisión
4. **Recertificar** al menos trimestralmente (`review.nextReview`)
5. **Desmantelar** conforme a un plan registrado

Trate los cambios del catálogo exactamente como código de aplicación: alcance, mínimo privilegio, higiene de secretos.

---

## Auditabilidad: el rastro documental

Cada cambio debe responder:
- ¿Qué agente lo produjo?
- ¿Qué servidores MCP y herramientas se usaron?
- ¿Qué operaciones de escritura fueron aprobadas y por quién?
- ¿Qué versión de la política estaba vigente?

Mecánica práctica: la clave del issue en el nombre de la rama, el uso de agentes y herramientas en la descripción del PR, entradas del catálogo versionadas y revisadas.

---

## Hoja de ruta de adopción

| Fase | Enfoque | Resultado |
| --- | --- | --- |
| 1. Barreras de protección | `copilot-instructions.md`, "No Sugerir", exclusión de contenido | Valores seguros por defecto en cada prompt |
| 2. Shift-left | Contenedores de desarrollo, hooks de pre-commit, prompts de modelado de amenazas | Fallos detectados en el IDE |
| 3. Pipeline | CodeQL, Autofix, escaneo de secretos, protección de push | Control objetivo de acceso |
| 4. HITL | Paridad en la revisión, listas de validación de paquetes | Responsabilidad humana |
| 5. Autonomía gobernada | Agentes, skills, catálogo MCP | Delegación auditable |

---

## Métricas que importan

- Secretos bloqueados por la protección de push (deberían tender a cero en el momento del commit)
- Tiempo medio de remediación de alertas de CodeQL; tasa de aceptación de Autofix
- Porcentaje de PRs que declaran uso de agentes y herramientas MCP
- Recomendaciones de paquetes alucinadas / rechazadas detectadas en revisión
- Entradas del catálogo MCP con la fecha `nextReview` vencida
- Excepciones concedidas en las puertas de aprobación, y por qué

Mida la **salud de la gobernanza**, no solo las líneas de código aceptadas.

---

## Conclusiones clave

1. Copilot optimiza para la **funcionalidad, no la seguridad** — no asuma nada
2. Ponga la restricción de seguridad **en el prompt** y en `copilot-instructions.md`
3. Bloquee el entorno: exclusión de contenido, contenedores de desarrollo, hooks de pre-commit
4. CodeQL, Autofix y la protección de push son los **guardianes objetivos**
5. Revise el código de IA como el de un tercero — y verifique que cada paquete existe
6. Trate toda salida de IA y todo contenido externo como **entrada no confiable**

---

# Preguntas

**Estructura de implementación sugerida**

- `.github/` — instrucciones, agentes y skills
- Una guía del ecosistema de agentes — modelo de delegación
- `mcp/` — catálogo y política de aprobación
- Un taller práctico para aplicar los patrones
