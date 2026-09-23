# Plan empresarial para implementar un Centro de Excelencia de GitHub Copilot y un SDLC agéntico

## 1. Propósito y alcance

Crear un **Centro de Excelencia (CoE) de GitHub Copilot y desarrollo agéntico** que permita a la empresa adoptar Copilot de forma segura, medible y reutilizable en todo el ciclo de vida del desarrollo de software (SDLC).

El CoE cubrirá:

- Gobernanza empresarial, riesgos, privacidad, propiedad intelectual y cumplimiento.
- Administración de GitHub Copilot, organizaciones, repositorios, políticas y acceso.
- Estándares de desarrollo, arquitectura, calidad, seguridad y operaciones.
- Instrucciones, prompts, skills, agentes personalizados y flujos de trabajo reutilizables.
- Capacitación, certificación, soporte y gestión del cambio.
- Evaluación de productividad, calidad, seguridad, costo y adopción.
- Un **portal empresarial de activos Copilot**, inspirado en [awesome-copilot](https://github.com/github/awesome-copilot), donde los equipos puedan descubrir, reutilizar, proponer, revisar, versionar y retirar estándares empresariales.

El CoE debe utilizar el marco NIST AI RMF —[Gobernar, Mapear, Medir y Gestionar](https://www.nist.gov/itl/ai-risk-management-framework)— como estructura de gestión de riesgos.

## 2. Resultados esperados

Al finalizar el primer año, la empresa deberá contar con:

1. Copilot habilitado mediante una configuración empresarial gobernada.
2. Un catálogo de repositorios, dominios, modelos, agentes, skills, prompts e instrucciones aprobados.
3. Un marco de desarrollo común para producir software con asistencia de IA.
4. Controles técnicos que impidan el uso no autorizado de datos, herramientas y entornos.
5. Evaluaciones reproducibles para aprobar, escalar o retirar activos Copilot.
6. Un portal interno de autoservicio con contribución controlada y trazabilidad.
7. Métricas que demuestren valor sin sacrificar calidad, seguridad, confiabilidad ni bienestar del desarrollador.

## 3. Modelo operativo del CoE

### 3.1 Patrocinio y órganos de decisión

El patrocinador debe ser el CIO, CTO o Chief Digital Officer. El CoE debe operar con tres órganos:

| Órgano | Responsabilidad | Cadencia |
|---|---|---|
| Comité ejecutivo | Presupuesto, apetito de riesgo, prioridades y excepciones | Trimestral |
| Consejo de estándares | Revisión técnica de frameworks, prompts, skills, instrucciones y agentes | Quincenal |
| Comunidad de práctica | Casos de uso, aprendizaje, soporte y propuestas de mejora | Mensual |

### 3.2 Equipo central

| Rol | Responsabilidad |
|---|---|
| Director del CoE | Estrategia, presupuesto, riesgos y resultados |
| Product manager | Roadmap, catálogo y priorización de capacidades |
| Administrador Copilot/GitHub | Políticas, licencias, organizaciones, permisos y métricas |
| Arquitecto empresarial | Arquitectura de referencia y patrones aprobados |
| Líder de seguridad de IA | Amenazas, mínimo privilegio, secretos, datos y abuso de herramientas |
| Líder de ingeniería de plataforma | Plantillas, CI/CD, entornos y automatización |
| Líder de calidad | Pruebas, validación, evaluación y puertas de calidad |
| Responsable de conocimiento | Portal, taxonomía, búsqueda, versionado y curación |
| Legal, privacidad y cumplimiento | IP, datos personales, regulación y contratos |
| Gestión del cambio | Capacitación, comunicaciones, adopción y certificación |

Cada dominio debe designar un **AI/Copilot Champion** responsable de aplicar los estándares y devolver retroalimentación al CoE.

## 4. Gobernanza empresarial de Copilot

### 4.1 Política y clasificación de riesgo

Clasificar los casos de uso y activos en cinco niveles:

| Nivel | Ejemplo | Requisitos |
|---|---|---|
| 1 — Asistivo | Explicación de código, documentación y aprendizaje | Revisión del usuario y registro estándar |
| 2 — Cambio acotado | Pruebas, refactorizaciones pequeñas y pull requests en borrador | Aislamiento, CI obligatorio y revisión humana |
| 3 — Sensible | Sistemas con datos regulados o correcciones de seguridad | Aprobación de seguridad, datos restringidos y auditoría ampliada |
| 4 — Alto impacto | Producción, pagos, identidad o decisiones de clientes | Autorización explícita, segregación de funciones y validación independiente |
| 5 — Prohibido inicialmente | Despliegue irreversible no revisado o acceso ilimitado | No permitido durante la fase inicial |

### 4.2 Controles mínimos

- SSO, MFA, grupos empresariales y mínimo privilegio.
- Separación entre usuarios, agentes, repositorios y entornos.
- Revisión de proveedores, modelos, extensiones y servidores MCP.
- Prohibición de secretos, credenciales y datos sensibles en prompts o archivos no autorizados.
- Registro de usuario, agente, modelo, herramientas, cambios, aprobaciones y resultados.
- Protección de ramas, CODEOWNERS y revisiones obligatorias.
- Escaneo de secretos, dependencias, vulnerabilidades, licencia e infraestructura.
- Proceso formal de excepciones, incidentes, suspensión y retiro.
- Revisión trimestral de permisos y activos.

Las exclusiones de contenido deben configurarse para impedir que Copilot acceda a archivos definidos por la empresa; deben documentarse sus limitaciones por superficie, especialmente para escenarios de agente. Consultar la [documentación oficial de exclusión de contenido](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/context/content-exclusion) antes de definir la política.

### 4.3 RACI de decisiones

- **CoE:** define estándares, patrones y criterios de aprobación.
- **Seguridad:** aprueba riesgos técnicos, datos, herramientas y agentes sensibles.
- **Legal/privacidad:** aprueba usos de propiedad intelectual, datos personales y regulación.
- **Equipo de producto:** acepta el valor y el riesgo del caso de uso.
- **Equipo de desarrollo:** implementa, prueba y mantiene el activo.
- **Revisor humano:** responde por el cambio de código aprobado.
- **Operaciones:** autoriza y supervisa acciones en producción.

## 5. Marco de desarrollo empresarial asistido por Copilot

El CoE debe publicar un **Enterprise Copilot Development Framework (ECDF)** obligatorio para equipos que utilicen Copilot.

### 5.1 Flujo estándar

1. **Descubrir:** definir problema, usuarios, datos, riesgos y resultado esperado.
2. **Planificar:** crear historias, criterios de aceptación, dependencias y plan técnico.
3. **Diseñar:** seleccionar patrones aprobados, documentar decisiones y modelar amenazas.
4. **Implementar:** usar instrucciones y skills del portal; limitar el alcance del agente.
5. **Verificar:** ejecutar pruebas deterministas, análisis de seguridad, accesibilidad y calidad.
6. **Revisar:** exigir revisión humana, evidencia de pruebas y trazabilidad de asistencia de IA.
7. **Liberar:** cumplir controles de cambio, aprobación, rollback y segregación de funciones.
8. **Operar:** monitorear, gestionar incidentes, medir resultados y actualizar el conocimiento.
9. **Aprender:** capturar lecciones, actualizar activos y retirar patrones obsoletos.

### 5.2 Estándares técnicos

El framework debe incluir plantillas y reglas para:

- Arquitectura, API, datos, integración, observabilidad y resiliencia.
- Convenciones de código y estructura de repositorios.
- Desarrollo seguro, privacidad por diseño y accesibilidad.
- Pruebas unitarias, integración, contrato, E2E, rendimiento y mutación.
- CI/CD, infraestructura como código, SBOM y gestión de dependencias.
- Pull requests, CODEOWNERS, branch protection y revisiones.
- Documentación, ADR, changelog y notas de versión.
- Estrategias de despliegue, rollback y respuesta a incidentes.

Para un piloto de aplicación web de bajo riesgo, el baseline debe incluir el lenguaje y framework principales, HTML, CSS, pruebas, accesibilidad, dependencias, documentación y pull requests; no debe incluir despliegue autónomo en producción.

## 6. Arquitectura de instrucciones, prompts, skills y agentes

### 6.1 Instrucciones

Las instrucciones definen estándares persistentes y deben aplicarse según alcance:

- **Empresariales:** políticas, seguridad, privacidad y normas comunes.
- **Organizacionales:** prácticas de una unidad o dominio.
- **De repositorio:** arquitectura, comandos, convenciones y pruebas locales.
- **Por ruta o tecnología:** reglas específicas para frontend, backend, infraestructura o documentación.

Las instrucciones deben ser breves, comprobables, versionadas y contener ejemplos de buenas y malas prácticas. Las instrucciones de repositorio pueden configurarse en Markdown y aplicarse a Copilot code review y cloud agent según su front matter y superficie; revisar la [documentación de instrucciones personalizadas de repositorio](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions).

### 6.2 Prompts

El estándar empresarial de prompts debe definir:

- Objetivo y contexto mínimo.
- Rol del agente y límites de autoridad.
- Entradas, supuestos y fuentes permitidas.
- Pasos de análisis y criterios de éxito.
- Formato de salida y evidencia requerida.
- Comportamiento ante incertidumbre.
- Datos prohibidos y acciones que requieren aprobación.
- Casos de prueba y ejemplos de uso.

Los prompts aprobados deben tener propietario, versión, fecha de revisión, nivel de riesgo, métricas y advertencias de seguridad. No deben contener secretos, datos personales innecesarios ni instrucciones que permitan evadir controles.

### 6.3 Skills

Una skill debe ser una capacidad reutilizable para una tarea específica y repetible, con instrucciones y recursos asociados. El estándar empresarial debe exigir:

```text
.github/skills/<nombre-de-la-skill>/
├── SKILL.md
├── references/
├── templates/
├── scripts/
└── tests/
```

`SKILL.md` debe incluir front matter con `name` y `description`, disparadores claros, prerrequisitos, flujo paso a paso, límites, resultados esperados, solución de problemas y referencias. Las skills deben validarse antes de publicarse porque pueden incluir instrucciones ocultas, inyecciones de prompt o scripts maliciosos; consultar [Adding agent skills for GitHub Copilot](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills) y las [guías de skills de awesome-copilot](https://github.com/github/awesome-copilot/blob/main/instructions/agent-skills.instructions.md).

### 6.4 Agentes personalizados

Cada agente debe declarar:

- Identidad, objetivo y alcance.
- Responsabilidades y tareas excluidas.
- Herramientas permitidas y permisos mínimos.
- Fuentes de contexto autorizadas.
- Pasos de trabajo y puntos de aprobación.
- Criterios de calidad y formato de salida.
- Manejo de errores, incertidumbre y escalamiento.
- Casos de prueba y métricas.
- Propietario, versión, nivel de riesgo y fecha de retiro.

La plantilla empresarial debe seguir una estructura explícita de identidad, responsabilidades, método, restricciones y resultados, coherente con las [guías de agentes de awesome-copilot](https://github.com/github/awesome-copilot/blob/main/instructions/agents.instructions.md).

Los agentes que modifiquen código deben crear cambios revisables, ejecutar las validaciones disponibles y nunca aprobar su propio trabajo.

## 7. Portal empresarial de estándares Copilot

### 7.1 Propósito

Crear un portal interno similar a [awesome-copilot](https://github.com/github/awesome-copilot), pero con contenido empresarial aprobado, búsqueda, contribución, validación y trazabilidad.

### 7.2 Catálogo

El portal debe organizar:

- Instrucciones por lenguaje, framework, repositorio y dominio.
- Prompts por etapa del SDLC y caso de uso.
- Skills con recursos, scripts y ejemplos.
- Agentes personalizados.
- Plantillas de repositorio y workflows.
- Patrones de arquitectura y seguridad.
- Checklists de revisión y evaluación.
- Guías de migración, troubleshooting y capacitación.
- Casos de éxito, métricas y anti-patrones.

Cada activo debe mostrar:

- Nombre, descripción y etiquetas.
- Nivel de riesgo y superficies compatibles.
- Responsable y equipo mantenedor.
- Versión, changelog y fecha de revisión.
- Estado: propuesta, experimental, aprobado, deprecated o retirado.
- Dependencias y permisos.
- Evidencia de pruebas y aprobaciones.
- Instrucciones de instalación o uso.
- Enlaces a repositorios, issues y documentación.

### 7.3 Flujo de contribución

1. El equipo crea una propuesta desde una plantilla.
2. Validaciones automáticas comprueban formato, metadatos, secretos, licencias, enlaces y seguridad.
3. El propietario del dominio revisa utilidad y mantenibilidad.
4. Seguridad y privacidad revisan el nivel de riesgo.
5. El Consejo de estándares aprueba o devuelve cambios.
6. Un workflow publica el activo en el portal con su versión.
7. Los usuarios pueden valorar, comentar, reportar problemas y proponer mejoras.
8. El mantenedor revisa métricas y caducidad periódicamente.

### 7.4 Arquitectura propuesta

- Repositorio central para fuentes y metadatos.
- GitHub Pages o portal web interno para el catálogo.
- GitHub Actions para validación, publicación y controles.
- CODEOWNERS para aprobación por dominio.
- JSON/YAML front matter para indexación y búsqueda.
- Versionado semántico y releases.
- Etiquetas de lenguaje, framework, riesgo y SDLC.
- Integración opcional con un registro interno de agentes y una base de métricas.
- Exportación de assets aprobados hacia repositorios de equipos mediante workflows o CLI.

El portal no debe ser un repositorio de contenido sin control: debe funcionar como una **cadena de suministro de conocimiento y comportamiento para agentes**.

## 8. Proceso de aprobación y ciclo de vida de activos

### Puertas obligatorias

- Validación de sintaxis y esquema.
- Detección de secretos y datos sensibles.
- Análisis de scripts y dependencias.
- Revisión de inyección de prompt y abuso de herramientas.
- Pruebas con casos representativos.
- Revisión de propiedad intelectual y licencias.
- Aprobación del propietario y del nivel de riesgo correspondiente.

### Ciclo de vida

`Propuesta → Experimental → Validación → Aprobado → Mantenimiento → Deprecated → Retirado`

Los activos de alto riesgo deben tener caducidad, revisión trimestral y mecanismo de revocación. Las skills descargadas de fuentes externas deben fijarse a una versión o SHA y pasar por validación interna antes de su uso.

## 9. Seguridad, privacidad y cumplimiento

Aplicar controles contra:

- Inyección de prompts.
- Divulgación de información sensible.
- Salidas inseguras ejecutadas sin validación.
- Agencia excesiva y escalamiento de privilegios.
- Dependencias y scripts maliciosos.
- Manipulación de contexto o repositorios.
- Código vulnerable o con licencias incompatibles.
- Uso de agentes en sistemas regulados sin aprobación.

Todo resultado generado por Copilot debe pasar por validaciones de software tradicionales. Los agentes no deben recibir credenciales permanentes; las acciones sobre producción deben utilizar identidades de corta duración, permisos mínimos, aprobación y rollback.

## 10. Capacitación y habilidades

Crear rutas por rol:

- **Desarrollador:** fundamentos de Copilot, prompting, revisión de código, pruebas y seguridad.
- **Tech lead/arquitecto:** diseño con IA, evaluación, límites de agentes y decisiones técnicas.
- **QA:** generación de pruebas, evaluación de cobertura, mutación y validación de salidas.
- **Seguridad:** amenazas de LLM, permisos, secretos, auditoría y respuesta a incidentes.
- **Product owner:** requisitos, criterios de aceptación, priorización y riesgos.
- **Administrador:** políticas, licencias, organizaciones, exclusiones y métricas.
- **Mantenedor de assets:** estructura, versionado, pruebas y publicación en el portal.

El programa debe incluir laboratorios, certificación interna, office hours, ejemplos por stack, guía de revisión de código generado y una comunidad de práctica.

## 11. Métricas y evaluación

### Valor de entrega

- Tiempo de ciclo de pull requests.
- Tiempo de entrega de cambios.
- Frecuencia de despliegue.
- Tareas completadas por agente.
- Tiempo ahorrado validado por el equipo.

### Calidad y seguridad

- Defectos escapados.
- Fallos de cambios y reversiones.
- Vulnerabilidades introducidas.
- Cobertura y mutación de pruebas.
- Incidentes de secretos o datos.
- Tasa de cambios rechazados en revisión.

### Uso responsable

- Tasa de intervención y override humano.
- Acciones fuera de permisos.
- Integridad de auditoría.
- Excepciones de política.
- Activos sin revisión vigente.
- Costo por tarea completada.

No declarar éxito por mayor velocidad si aumentan defectos, incidentes, deuda técnica o agotamiento del equipo.

## 12. Hoja de ruta

### Fase 0 — Movilizar
**Semanas 1–4**

- Aprobar estatuto, presupuesto y órgano de decisión.
- Inventariar licencias, herramientas, agentes, prompts, skills e instrucciones existentes.
- Clasificar repositorios y datos por riesgo.
- Definir el ECDF y los estándares mínimos.
- Seleccionar una aplicación representativa como piloto de bajo riesgo.
- Diseñar la taxonomía y el esquema de metadatos del portal.

### Fase 1 — Establecer controles
**Meses 2–3**

- Configurar políticas empresariales de Copilot.
- Definir grupos, permisos, exclusiones, proveedores y modelos aprobados.
- Publicar plantillas para instrucciones, prompts, skills y agentes.
- Configurar repositorio central, CODEOWNERS, validaciones y portal mínimo.
- Establecer métricas iniciales y registro de auditoría.

### Fase 2 — Pilotar
**Meses 4–6**

- Probar agentes de documentación, pruebas, accesibilidad, dependencias y mantenimiento.
- Publicar los primeros assets empresariales en el portal.
- Medir calidad, seguridad, adopción, costo y tiempo ahorrado.
- Ejecutar revisiones de seguridad y ejercicios de inyección de prompt.
- Decidir qué activos se escalan, modifican o retiran.

### Fase 3 — Escalar por dominio
**Meses 7–9**

- Incorporar dominios mediante un onboarding repetible.
- Crear champions y revisores por dominio.
- Publicar skills y agentes por tecnología y etapa del SDLC.
- Integrar el portal con repositorios y plantillas de equipos.
- Establecer revisiones trimestrales de riesgo y valor.

### Fase 4 — Institucionalizar
**Meses 10–12**

- Automatizar evaluación continua y reportes de cumplimiento.
- Optimizar modelos, costos y permisos.
- Integrar métricas Copilot con ingeniería, seguridad y operaciones.
- Auditar el catálogo y retirar activos obsoletos.
- Publicar resultados y actualizar el roadmap anual.

## 13. Backlog inicial de 90 días

1. Aprobar el estatuto y la política empresarial de Copilot.
2. Definir riesgo, datos prohibidos y acciones no permitidas.
3. Inventariar activos Copilot y herramientas existentes.
4. Crear el repositorio del portal empresarial.
5. Definir el esquema de metadatos y la taxonomía.
6. Crear plantillas de instrucciones, prompts, skills y agentes.
7. Implementar validaciones de seguridad, formato y licencias.
8. Configurar CODEOWNERS, branch protection y CI.
9. Preparar el repositorio seleccionado como piloto.
10. Publicar cinco assets iniciales y probar su instalación.
11. Capacitar a los primeros champions y revisores.
12. Medir baseline y presentar la decisión de escalar o detener.

## 14. Criterios de éxito

El CoE será exitoso cuando:

- Cada agente y asset empresarial tenga propietario, versión, riesgo, permisos, evaluación y trazabilidad.
- Los equipos encuentren y reutilicen estándares aprobados desde el portal.
- Las contribuciones pasen por validaciones automáticas y revisiones responsables.
- Los equipos incorporen un asset aprobado en días, no meses.
- Copilot mejore el flujo de entrega sin aumentar defectos, vulnerabilidades o incidentes.
- Los desarrolladores sepan cuándo usar instrucciones, prompts, skills o agentes.
- Las acciones de alto riesgo estén técnicamente limitadas y requieran aprobación humana.
- El catálogo tenga métricas de uso, satisfacción, calidad, costo y vigencia.

## 15. Fuentes de referencia

- [GitHub Copilot: agregar instrucciones personalizadas de repositorio](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions)
- [GitHub Copilot: agregar skills de agentes](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills)
- [GitHub Copilot: exclusión de contenido](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/context/content-exclusion)
- [GitHub awesome-copilot](https://github.com/github/awesome-copilot)
- [Guías de skills de awesome-copilot](https://github.com/github/awesome-copilot/blob/main/instructions/agent-skills.instructions.md)
- [Guías de agentes de awesome-copilot](https://github.com/github/awesome-copilot/blob/main/instructions/agents.instructions.md)
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [OWASP Top 10 para aplicaciones LLM](https://genai.owasp.org/resource/owasp-top-10-for-llm-applications-2025/)
