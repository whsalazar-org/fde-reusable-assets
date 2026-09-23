# GitHub Copilot: El ecosistema moderno de desarrollo con IA

## Resumen

GitHub Copilot ya no es solo un autocompletado de código. Ahora es un ecosistema completo de desarrollo con IA compuesto por piezas conectadas: agentes, Copilot Chat, flujos agenticos, una CLI, skills, instrucciones, MCP y flujos de trabajo automatizados.


## 💬 Copilot Chat

**Copilot Chat** es la interfaz conversacional y en tiempo real integrada en tu IDE (VS Code, JetBrains, Visual Studio) y en GitHub.com. Es la forma más interactiva y inmediata de trabajar con Copilot.

Lo que puedes hacer con Copilot Chat:
- Hacer preguntas sobre tu base de código — *"¿Qué hace esta función?"* o *"¿Dónde se maneja la autenticación?"*
- Obtener explicaciones de código, ayuda para depuración y sugerencias en línea
- Solicitar ediciones multiarchivo usando **Copilot Edits** — describe el cambio en lenguaje natural y Copilot lo aplica a través de varios archivos
- Ejecutar **Agent Mode** dentro del IDE — Copilot toma un objetivo, planea pasos, edita archivos, ejecuta pruebas, lee errores e itera de forma autónoma *dentro de tu espacio de trabajo local*
- Obtener revisiones de código y sugerencias de refactorización bajo demanda

**Agent Mode** es lo que eleva el chat de preguntas y respuestas a algo mucho más potente. En Agent Mode, le das a Copilot un objetivo de alto nivel y este itera: edita, prueba, corrige y vuelve a intentar hasta completar la tarea.

> **Se usa mejor para:** trabajo interactivo y en tiempo real — prototipos, depuración, aprendizaje, exploración de una base de código, iteración rápida y tareas complejas que se benefician de la colaboración estrecha humano-IA.


## 🤖 Agentes de Copilot (Coding Agent)

Mientras Copilot Chat trabaja *contigo* en tiempo real, el **Copilot Coding Agent** trabaja *por ti* de forma asíncrona. Es un desarrollador de IA totalmente autónomo al que asignas tareas; luego se pone a trabajar y termina la tarea.

Piensa en ello así: **Chat es tu programador compañero. El Coding Agent es tu desarrollador junior.**

Los agentes se definen usando archivos `.agent.md` almacenados en `.github/agents/` dentro de tu repositorio (por ejemplo, `.github/agents/my-agent.agent.md`), o para agentes de nivel organizacional, en la carpeta `agents/` del `.github` de la organización.
- **Contexto completo del código** — lee tu código, documentación, issues y PRs anteriores
- **Acceso a herramientas** — crea ramas, escribe código y ejecuta pruebas a través de GitHub Actions
- **Flexibilidad de modelos** — configurable para distintos modelos de IA
- **Conexiones externas** — a través de servidores MCP declarados en el archivo del agente o en `.mcp.json` en la raíz del repositorio, enroutando llamadas a APIs externas, bases de datos, sistemas CI/CD y más

El flujo de trabajo del Coding Agent:
1. Recibe una tarea (a través de un Issue o del panel de Agents)
2. Crea una rama (`copilot/*`)
3. Escribe código, ejecuta compilaciones y pruebas en un entorno aislado
4. Abre un **draft PR** con un registro de la sesión sobre lo que hizo y por qué
5. Responde comentarios de revisión en el PR con nuevos commits
6. Espera a que lo apruebes y hagas merge — no puede fusionar su propio trabajo

Puedes invocar agentes desde GitHub.com, VS Code, GitHub Mobile o la Copilot CLI.

> **Se usa mejor para:** tareas bien acotadas y claramente descritas — correcciones de bugs, escritura de pruebas, refactorización, adición de funciones, generación de documentación — especialmente cuando deseas delegar y volver más tarde para revisar.


## 🔌 Servidores MCP (Model Context Protocol)

**MCP (Model Context Protocol)** es un estándar abierto que permite a los agentes de Copilot conectarse a herramientas, fuentes de datos y servicios externos en tiempo de ejecución, sin hardcodear integraciones ni volver a desplegar nada.

### Cómo funciona

Un **servidor MCP** expone tres tipos de capacidades a un agente:
- **Tools** — funciones invocables (por ejemplo, "ejecuta esta consulta", "crea este ticket")
- **Resources** — datos que el agente puede leer (por ejemplo, archivos, registros de base de datos, respuestas de API)
- **Prompts** — plantillas predefinidas para tareas comunes

Los agentes descubren y llaman a servidores MCP en tiempo de ejecución a través de un protocolo estandarizado, lo que significa que puedes agregar nuevas herramientas a un servidor MCP y los agentes las usarán de inmediato, sin necesidad de cambiar el agente.

### Dónde se configuran los servidores MCP

Los servidores MCP pueden declararse en dos lugares:

- **`.mcp.json` en la raíz del repositorio** — disponible para todos los agentes y para Copilot Chat (Agent Mode) dentro del repositorio
- **Frontmatter YAML de un archivo `.agent.md`** — acotado a un agente personalizado específico

```jsonc
// .mcp.json
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/",
      "headers": { "Authorization": "Bearer ${GITHUB_TOKEN}" }
    },
    "internal-docs": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@company/docs-mcp-server"]
    }
  }
}
```

```yaml
# .github/agents/my-agent.agent.md

name: Security Reviewer
description: Reviews PRs for security vulnerabilities
tools: [read, web-search]
mcp-servers:
  snyk:
    type: http
    url: "https://mcp.snyk.io/sse"
    headers:
      Authorization: "Bearer ${SNYK_TOKEN}"

You are a security expert. When reviewing code, check for...
```

### MCP en el ecosistema más amplio de Copilot

MCP es la capa conectiva entre Copilot y el resto de tu cadena de herramientas:

| Superficie | Cómo se usa MCP |
| --- | --- |
| **Copilot Chat (Agent Mode)** | Se conecta a servidores MCP mediante `.mcp.json` para acceso a herramientas en tiempo real dentro del IDE |
| **Copilot Coding Agent** | Usa servidores MCP declarados en `.agent.md` o `.mcp.json` durante la ejecución asíncrona de tareas |
| **Agentic Workflows (`gh-aw`)** | Usa el MCP Gateway (`gh-aw-mcpg`) para enrutar llamadas de herramientas a través de una pasarela centralizada y auditada |
| **Copilot CLI** | Puede conectarse a servidores MCP para flujos de trabajo agenticos basados en terminal |

> **Se usa mejor para:** conectar Copilot a sistemas que no conoce de forma nativa — APIs internas, bases de datos empresariales, sistemas de tickets (Jira, Linear), plataformas de observabilidad, analizadores de seguridad, CI/CD y más.


## ⚡ Flujos de trabajo agenticos

**Agentic Workflows** son pipelines de automatización impulsados por IA — pero a diferencia de los flujos YAML tradicionales de CI/CD, están escritos en **Markdown plano**. Describes tu *intención* en lenguaje natural y el sistema se encarga del resto.

Características clave:
- **Adaptativos** — pueden tomar decisiones según el contexto, no solo según reglas predefinidas
- **Disparados** por eventos (PRs abiertos, horarios, etiquetas aplicadas, comentarios, etc.)
- **Seguros** — se ejecutan con permisos de solo lectura por defecto; todas las operaciones de escritura pasan por manejadores `safe-outputs` sanitizados
- **Accesibles** — no se requiere experiencia profunda en scripting; si puedes escribir un README, puedes escribir un flujo de trabajo
- **Multi-engine** — los flujos pueden apuntar a Copilot, Claude, Codex o Gemini como backend de IA

Ejemplo: un flujo de trabajo definido en Markdown que dice *"Cuando se etiqueta un issue como `bug`, analiza la base de código, identifica la causa raíz más probable y abre un draft PR con una solución propuesta."*

### 🛠️ Impulsados por `github/gh-aw`

La implementación oficial de GitHub Agentic Workflows vive en [**`github/gh-aw`**](https://github.com/github/gh-aw) — una extensión de `gh` CLI escrita en Go que es el motor detrás de todo el sistema.

**Los flujos de trabajo son archivos Markdown**, no YAML. Un flujo vive en `.github/workflows/my-workflow.md` y usa un encabezado de frontmatter YAML para definir disparadores, permisos y tipos de salida seguros; luego la compilación realiza la conexión con Actions.

```markdown
---
on:
  issues:
    types: [opened]
permissions:
  issues: read
timeout-minutes: 10
safe-outputs:
  create-issue:
  add-comment:
---

# Triage New Issues

When a new issue is opened, review it for completeness. If the issue is missing
reproduction steps or environment details, post a comment asking the reporter to
provide them. If it looks like a duplicate of an existing issue, link to the original.
```

Luego, **`gh aw compile`** compila este archivo Markdown en un archivo `.lock.yml` estándar de GitHub Actions que sí puede ejecutarse. Escribes en lenguaje natural; el compilador se encarga del plumbing de Actions.

**Conceptos clave de `gh-aw`:**

| Concepto | Descripción |
| --- | --- |
| **`safe-outputs`** | El límite seguro para todas las operaciones de escritura — crear issues, PRs, comentarios, etiquetas, actualizaciones de proyectos, etc. La IA propone; el framework lo sanitiza y ejecuta. |
| **`engine:`** | Qué backend de IA usar: `copilot` (predeterminado), `claude`, `codex` o `gemini` |
| **`network:`** | Lista de dominios permitidos/bloqueados que el agente puede acceder durante la ejecución |
| **`imports:`** | Reutiliza fragmentos compartidos entre flujos de trabajo (principio DRY) |
| **Agent Workflow Firewall (AWF)** | Proyecto complementario [`gh-aw-firewall`](https://github.com/github/gh-aw-firewall) para control de salida de red y registro de actividad |
| **MCP Gateway** | [`gh-aw-mcpg`](https://github.com/github/gh-aw-mcpg) enruta llamadas del Model Context Protocol a través de una pasarela unificada y auditada |

**Primeros pasos:**

```bash
# Instalar la extensión gh
gh extension install github/gh-aw

# Crear tu primer flujo de trabajo (guiado)
gh aw create

# Compilar flujos de trabajo a YAML de GitHub Actions
gh aw compile

# Ver el estado de todos los flujos de trabajo agenticos del repositorio
gh aw status
```

> **Se usa mejor para:** automatizar tareas repetitivas o adaptativas del repositorio — triaje, preparación de revisiones de código, actualizaciones de dependencias, generación de pruebas, notas de release — cuando quieres que la IA actúe sobre eventos con reglas inteligentes.


## 🖥️ Copilot CLI

**GitHub Copilot CLI** lleva Copilot a la terminal. Ejecuta `copilot` para iniciar su interfaz de terminal interactiva y usar prompts en lenguaje natural para hacer preguntas, inspeccionar código, hacer cambios, ejecutar herramientas y más.

Instálala con `npm install -g @github/copilot`. También se admite Homebrew, WinGet y el script de instalación.

Comandos representativos:
- `copilot` — inicia una sesión interactiva
- `copilot -p "..."` o `copilot --prompt="..."` — ejecuta un prompt de una sola vez de forma programática
- `copilot --plan` o `copilot --mode plan` — inicia modo plan
- `copilot --autopilot` — continúa de forma autónoma; revisa la configuración de confianza y aprobación antes de usarlo
- `copilot help [TOPIC]` — muestra ayuda del comando
- `copilot update` — actualiza la CLI
- `copilot completion SHELL` — genera un script de autocompletado para el shell

Comandos interactivos útiles incluyen `/login`, `/help`, `/plan`, `/model`, `/review`, `/pr`, `/mcp`, `/skills`, `/init` y `/feedback`.

Lo que la hace *agentica*:
- Soporta **sesiones interactivas** y ejecución programática de prompts
- Puede planificar trabajo de varios pasos en **plan mode** y continuar con **autopilot**
- Puede usar herramientas, servidores MCP, skills y agentes personalizados
- Usa el contexto del repositorio para entender y actuar sobre tu proyecto
- Proporciona permisos, aprobaciones y controles de confianza explícitos

> **Se usa mejor para:** desarrolladores que prefieren la terminal, entornos de servidor/contenedor sin GUI, scripting, explicaciones rápidas de comandos desconocidos y automatización basada en la terminal.

Referencias oficiales: [About Copilot CLI](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/about-copilot-cli) · [Using Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/...)

## 🧩 Skills

**Skills** son paquetes modulares y reutilizables de instrucciones y scripts que enseñan a los agentes *cómo hacer tareas específicas*. Son los bloques de construcción que expanden lo que un agente sabe hacer.

Una skill vive en una carpeta con un archivo `SKILL.md`:
- **Project skills:** `.github/skills/<name>/SKILL.md` — disponibles para todos en el repositorio
- **User skills:** `~/.copilot/skills/` — personales y portables entre proyectos
- **Org skills:** (próximamente) — compartidas entre varios repositorios de una organización

Las skills son agnósticas a los agentes — siguen una especificación abierta y pueden ser usadas por cualquier herramienta compatible. Se descubren y cargan automáticamente cuando el agente determina que son relevantes para el contexto actual.

> **Se usa mejor para:** codificar experiencia repetible — *"cómo escribir una migración", "cómo agregar un endpoint de API", "cómo crear una entrada de changelog"* — para que agentes y compañeros apliquen convenciones consistentes sin reinventar la solución cada vez.


## 📋 Copilot Instructions

**Copilot Instructions** son personalizaciones permanentes que moldean el comportamiento de Copilot en cada sesión dentro de un repositorio. Son archivos Markdown que definen estándares de codificación, convenciones del equipo y restricciones del proyecto.

Hay dos ámbitos:
- **`.github/copilot-instructions.md`** — aplica globalmente a todas las interacciones de Copilot en el repositorio
- **`.github/instructions/*.instructions.md`** — dirigidas a tipos o patrones de archivo específicos (por ejemplo, solo aplica a `*.ts`)

Las instrucciones son pasivas: no disparan automatización. Son contexto que Copilot *siempre lleva* cuando ayuda en este repositorio, ya sea en Chat, usando el Coding Agent o ejecutando la CLI.

> **Se usa mejor para:** imponer estándares del equipo (nomenclatura, patrones de prueba, elección de framework), proporcionar contexto de onboarding, definir restricciones arquitectónicas y asegurar calidad consistente en el código.


## 🎫 Cómo encajan los issues de GitHub

Los issues son el **punto de entrada** para el flujo agentico del Copilot Coding Agent. Sirven como "órdenes de trabajo" estructuradas que tanto humanos como agentes pueden consumir.

**El ciclo de vida:**

1. **Crear un issue bien escrito** — describe el problema, criterios de aceptación y contexto relevante
2. **Asignar a `@copilot`** — en GitHub.com, en VS Code o desde el panel de Agents
3. **Copilot analiza y planea** — lee el issue, revisa el código, forma un plan
4. **El agente ejecuta en una rama nueva** — escribe código, ejecuta pruebas, corrige errores de linting
5. **Se abre un draft PR** — ves el trabajo del agente y un registro de decisiones
6. **Iterar mediante comentarios del PR** — deja feedback en el PR; el agente responde con nuevos commits
7. **Revisas y haces merge** — Copilot no puede aprobar ni hacer merge de sus propios PRs; las personas siguen en control

**Consejos para issues útiles:**
- Sé específico y acotado — *"Corrige null pointer en `UserService.getById()`"* supera a *"arregla el bug del usuario"*
- Incluye criterios de aceptación — el agente los usa como checklist
- Agrega capturas de pantalla, mensajes de error o logs — Copilot los lee
- Una vez abierto el PR, **deja feedback en el PR** (no en el issue original)

> **Se usa mejor para:** delegar tareas bien definidas y acotadas al Coding Agent — adición de funciones, corrección de bugs, refactorización, cobertura de pruebas, documentación.

## 🗺️ Cuándo usar cuál

| Situación | Usa esto |
| --- | --- |
| Quieres que Copilot siga el estilo de codificación de tu equipo en cada sesión | **Copilot Instructions** |
| Quieres enseñar a Copilot una tarea repetitiva que tu equipo hace a menudo | **Skills** |
| Quieres chatear, depurar, explorar o prototipar de forma interactiva | **Copilot Chat** |
| Quieres que Copilot trabaje de forma autónoma paso a paso en tu IDE | **Copilot Chat – Agent Mode** |
| Tienes una tarea claramente acotada y quieres que la haga de forma asíncrona | **Issues → Coding Agent** |
| Quieres automatizar un proceso del repositorio al ocurrir eventos (triaje, revisiones, releases) | **Agentic Workflows (`gh-aw`)** |
| Necesitas un asistente de IA especializado para un rol concreto (seguridad, docs, etc.) | **Agente personalizado** |
| Quieres conectar Copilot a una herramienta externa, API o fuente de datos | **Servidor MCP** |
| Estás en la terminal y necesitas ayuda con comandos o automatización basada en terminal | **Copilot CLI** |


## 🔗 Cómo se conectan todo

```
Instructions          (contexto permanente para cada sesión)
    ↓
Skills                (especialización modular cargada bajo demanda)
    ↓
MCP Servers           (herramientas externas, APIs y fuentes de datos conectadas en tiempo de ejecución)
    ↓
Agents                (personas especializadas que usan instructions + skills + MCP)
    ↓
┌─────────────────────────────────────────────────┐
│  Copilot Chat          │  Copilot Coding Agent  │
│  (tiempo real, contigo) │  (asíncrono, por ti)   │
│  IDE / GitHub.com      │  GitHub.com / Cloud    │
└─────────────────────────────────────────────────┘
    ↓                            ↑
Copilot CLI           Issues (la orden de trabajo)
(soporte terminal a   (disparador para el Coding Agent)
 todo lo anterior)
    ↓
Agentic Workflows (gh-aw)
(automatización basada en eventos — flujos Markdown compilados
 a GitHub Actions, con safe-outputs, guardrails de red,
 MCP Gateway y soporte multi-engine)
```

Todo el sistema está diseñado alrededor de una idea simple: **describes lo que quieres, Copilot averigua cómo hacerlo y tú sigues teniendo el control de lo que se publica**.

- Usa **Chat** cuando quieras trabajar *junto a* Copilot en el momento
- Usa el **Coding Agent** cuando quieras delegar y revisar de forma asíncrona
- Usa **flujos `gh-aw`** cuando quieras que ocurra automáticamente según eventos del repositorio
- Usa **servidores MCP** para conectar Copilot a herramientas y datos fuera de GitHub
- Usa la **CLI** cuando vivas en la terminal
- Usa **Instructions + Skills** para hacer que todo lo anterior sea más consistente, contextualizado y reutilizable

Usados juntos, estas herramientas te permiten elegir el nivel correcto de ayuda con IA para cada tarea, desde colaboración práctica hasta ejecución totalmente automatizada.
