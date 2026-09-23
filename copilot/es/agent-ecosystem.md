# Ecosistema de agentes, MCP y agentes personalizados

Este documento define una estructura práctica para usar agentes de IA, servidores del Model Context Protocol (MCP) y agentes personalizados en un repositorio de software. El objetivo es mantener la automatización útil, segura y observable, con límites claros y responsabilidad humana cuando corresponda.

## 1. Conceptos fundamentales

| Componente | Responsabilidad | Ámbito recomendado |
| --- | --- | --- |
| **Agente principal** | Comprende la solicitud, planifica el trabajo, delega cuando resulta útil y es responsable del resultado final. | Repositorio o espacio de trabajo |
| **Agente personalizado** | Proporciona conocimientos especializados, reglas, herramientas y un flujo de trabajo acotado. | Una funcionalidad, disciplina o tarea recurrente |
| **Subagente** | Realiza una parte aislada del trabajo para un agente principal o personalizado. | Una tarea específica |
| **Host/cliente MCP** | Conecta una aplicación de IA con servidores MCP y administra su ciclo de vida y permisos. | Tiempo de ejecución del usuario o de la aplicación |
| **Servidor MCP** | Expone herramientas y datos externos mediante un protocolo estándar. | Una integración o dominio |
| **Habilidad o conjunto de instrucciones** | Proporciona orientación procedimental reutilizable, como pruebas o preparación de lanzamientos. | Flujo de trabajo entre agentes |

MCP utiliza una arquitectura de host, cliente y servidor. El host coordina uno o más clientes, y cada cliente se conecta a un servidor MCP con capacidades y límites explícitos. Consulta la [arquitectura de MCP](https://modelcontextprotocol.io/specification/2025-06-18/architecture).

## 2. Estructura recomendada del repositorio

```text
.github/
├── agents/
│   ├── frontend-specialist.agent.md
│   ├── test-specialist.agent.md
│   ├── security-reviewer.agent.md
│   └── documentation-specialist.agent.md
├── copilot-instructions.md
└── workflows/
    └── agent-validation.yml

docs/
└── agent-ecosystem.md

mcp/
├── README.md
├── catalog/
│   ├── github.json
│   ├── browser.json
│   └── observability.json
└── policies/
    └── approved-tools.md
```

Usa `.github/agents/` para los agentes personalizados a nivel de repositorio. Mantén los perfiles de agentes breves, específicos y versionados junto con el código al que dan soporte. GitHub documenta los perfiles a nivel de repositorio en [Acerca de los agentes personalizados de GitHub](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-custom-agents).

Al adoptar esta estructura, mantén versionados en conjunto los perfiles de agentes personalizados, el catálogo de MCP y la política de herramientas aprobadas. Agrega únicamente los perfiles y las integraciones que el repositorio utilice.

## 3. Arquitectura por capas

### Capa 1: Instrucciones compartidas del repositorio

`.github/copilot-instructions.md` debe contener las reglas que se aplican a todos los agentes:

- Propósito y arquitectura del proyecto.
- Convenciones de React y JavaScript.
- Comandos de pruebas y validación.
- Requisitos de seguridad y privacidad.
- Expectativas de nomenclatura, documentación y revisión.
- Acciones prohibidas explícitamente, como confirmar secretos o modificar archivos no relacionados.

Mantén estas instrucciones estables y evita incluir aquí flujos de trabajo especializados.

### Capa 2: Agentes personalizados

Cada agente personalizado debe tener una responsabilidad clara. Un buen perfil de agente incluye:

1. **Nombre y descripción** — qué hace el agente y cuándo debe utilizarse.
2. **Límites** — archivos, sistemas y tareas que puede modificar.
3. **Flujo de trabajo** — inspeccionar, planificar, implementar, probar e informar.
4. **Herramientas permitidas** — únicamente las necesarias para su trabajo.
5. **Controles de calidad** — pruebas, linting, verificaciones de seguridad o criterios de revisión.
6. **Contrato de salida** — un resumen predecible de cambios, validación y riesgos.

División sugerida de responsabilidades:

- `frontend-specialist`: componentes React, accesibilidad, comportamiento del navegador y pruebas de interfaz.
- `test-specialist`: diseño y ejecución de pruebas, fixtures y brechas de cobertura.
- `security-reviewer`: revisión de dependencias, secretos, validación de entradas y autorización.
- `documentation-specialist`: Markdown, uso de API, notas de arquitectura y ejemplos.
- `release-reviewer`: alcance de cambios, versionado, registro de cambios y preparación para el despliegue.

Los agentes personalizados pueden utilizarse como subagentes aislados, lo que permite al agente principal delegar tareas específicas sin sobrecargar su contexto. Consulta [Agentes personalizados y orquestación de subagentes](https://docs.github.com/en/copilot/how-tos/copilot-sdk/features/custom-agents).

### Capa 3: Servidores MCP

Considera los servidores MCP como proveedores de capacidades, no como tomadores de decisiones autónomos. Cada servidor debe tener:

- Un único dominio y un responsable claro.
- Un propósito documentado y una clasificación de datos.
- Herramientas permitidas explícitamente.
- Herramientas de solo lectura habilitadas de forma predeterminada cuando sea posible.
- Autenticación mediante variables de entorno o secretos administrados.
- Tiempos de espera, límites de frecuencia y comportamiento ante fallos.
- Orientación para auditoría y monitoreo.
- Una política de versiones y compatibilidad.

Ejemplos:

| Servidor | Propósito | Acceso predeterminado |
| --- | --- | --- |
| GitHub | Issues, pull requests y metadatos del repositorio | Solo lectura; las operaciones de escritura requieren un flujo deliberado |
| Browser o Playwright | Inspección de interfaz y pruebas de navegador | Solo entornos de prueba |
| Observabilidad | Registros, trazas y métricas | Solo lectura, con datos redactados |
| Sistema de diseño | Orientación sobre componentes y tokens | Solo lectura |
| Base de datos | Inspección de esquemas o datos de prueba | Nunca acceso de escritura en producción |

En las configuraciones de repositorio de GitHub Copilot, las herramientas MCP pueden ejecutarse de forma autónoma. Por lo tanto, prefiere listas de herramientas específicas y de solo lectura, y no expongas acceso amplio de escritura de forma predeterminada. Consulta [Configurar servidores MCP para un repositorio](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers).

## 4. Modelo de delegación

Usa este flujo de decisión:

1. El agente principal clasifica la solicitud.
2. Si la tarea es simple y local, el agente principal la gestiona directamente.
3. Si la tarea requiere conocimientos especializados, delega en un agente personalizado.
4. Si la tarea requiere datos o acciones externas, usa la capacidad MCP más pequeña que la satisfaga.
5. Si se necesitan varias investigaciones independientes, ejecuta subagentes en paralelo.
6. El agente principal revisa todos los resultados, resuelve conflictos, ejecuta la validación final y es responsable de la respuesta o pull request.

Evita cadenas más largas de lo necesario. Un flujo predeterminado útil es:

```text
Solicitud del usuario
    ↓
Agente principal
    ├── Agente personalizado: implementación
    ├── Agente personalizado: pruebas
    └── Agente personalizado: revisión de seguridad
             ↓
      Servidores MCP usados solo cuando sea necesario
             ↓
El agente principal valida e informa
```

## 5. Seguridad y gobernanza

- Nunca incluyas directamente claves de API, tokens, contraseñas, cadenas de conexión ni datos personales.
- Usa secretos administrados por el repositorio, la organización o el entorno.
- Concede únicamente las herramientas y permisos MCP mínimos necesarios.
- Separa las integraciones de desarrollo, prueba, staging y producción.
- Prefiere datos sintéticos o redactados para los flujos de trabajo de agentes.
- Exige revisión humana para acciones destructivas, cambios en producción, migraciones y comunicaciones externas.
- Fija o restringe las versiones de dependencias y servidores cuando sea práctico.
- Registra el responsable, propósito, acceso a datos y estado de aprobación de cada servidor MCP.
- Revisa los perfiles de agentes y las configuraciones MCP como si fueran código de la aplicación.

GitHub admite políticas y registros MCP para organizaciones y empresas, con el fin de seleccionar servidores aprobados y restringir el acceso. Consulta [Uso de servidores MCP en tu empresa](https://docs.github.com/en/copilot/concepts/mcp-management).

## 6. Plantilla de perfil de agente

```markdown
---
name: frontend-specialist
description: Builds and reviews small, accessible React features.
tools:
  - read
  - search
  - edit
  - test
---

You are the frontend specialist for this repository.

## Scope
- Modify React and JavaScript files required by the request.
- Keep components small, functional, and focused.
- Do not change backend, deployment, or unrelated configuration files.

## Workflow
1. Inspect the existing component and test patterns.
2. State a short implementation plan.
3. Make the smallest complete change.
4. Add or update tests when infrastructure exists.
5. Run the relevant checks.
6. Report files changed, checks run, and remaining risks.

## Quality rules
- Preserve accessibility and keyboard behavior.
- Avoid unnecessary dependencies.
- Never add secrets or environment-specific credentials.
```

El frontmatter exacto compatible con las interfaces de GitHub puede variar, por lo que debes validar los perfiles según la documentación del entorno objetivo. Consulta [Configuración de agentes personalizados](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-custom-agents).

## 7. Lista de verificación para registrar un servidor MCP

Antes de aprobar un servidor MCP, confirma lo siguiente:

- [ ] El responsable y el propósito empresarial están documentados.
- [ ] Se conocen el origen y la versión del servidor.
- [ ] Las herramientas están permitidas explícitamente.
- [ ] El acceso de solo lectura es el predeterminado.
- [ ] Los secretos se referencian mediante variables administradas y nunca se confirman en el repositorio.
- [ ] Los campos sensibles se redactan en las respuestas y los registros.
- [ ] Se comprenden el acceso de red y la residencia de los datos.
- [ ] El comportamiento ante fallos, tiempos de espera y reintentos está documentado.
- [ ] Existe un entorno de prueba o sandbox.
- [ ] Se define una fecha de revisión y un proceso de desactivación.

## 8. Estándares para pull requests y mantenimiento

Todo cambio creado por un agente debe incluir:

- Un título y una descripción de pull request enfocados.
- Pruebas o una explicación clara cuando no sean aplicables.
- Una lista de los servidores MCP y las herramientas con capacidad de escritura utilizadas.
- Consideraciones de seguridad y privacidad.
- Cualquier trabajo posterior o limitación conocida.

Revisa el ecosistema trimestralmente, o antes si cambia una herramienta, proveedor, permiso o clasificación de datos. Elimina los agentes y servidores MCP que ya no se utilicen, en lugar de permitir la proliferación innecesaria de capacidades.

## Referencias

- [Arquitectura del Model Context Protocol](https://modelcontextprotocol.io/specification/2025-06-18/architecture)
- [Acerca de los agentes personalizados de GitHub](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-custom-agents)
- [Agentes personalizados y orquestación de subagentes](https://docs.github.com/en/copilot/how-tos/copilot-sdk/features/custom-agents)
- [Configurar servidores MCP para un repositorio](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers)
- [Gobernanza de servidores MCP](https://docs.github.com/en/copilot/concepts/mcp-management)
