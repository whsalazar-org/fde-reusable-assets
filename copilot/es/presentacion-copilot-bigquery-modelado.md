# Usar Copilot para acelerar el modelado en BigQuery

## Título

### Artefactos de modelado, mapeos de campos y esquemas tipados

**Mensaje clave:** acelerar la primera versión sin delegar las decisiones de negocio, seguridad o despliegue.

---

## Objetivos

- Definir un contrato de modelado antes de generar SQL.
- Crear mapeos origen-destino trazables.
- Generar esquemas tipados para BigQuery y aplicaciones.
- Producir modelos SQL y pruebas de calidad.
- Aplicar un ciclo de revisión seguro y repetible.

---

## Principio de trabajo

### Copilot acelera la ejecución; las personas gobiernan las decisiones

- Copilot crea borradores y detecta inconsistencias.
- Puede proponer validaciones, documentación y pruebas.
- Puede identificar campos sensibles y preguntas abiertas.
- No debe inventar definiciones de negocio.
- No debe aprobar accesos ni ejecutar cambios productivos sin autorización.

---

## Artefactos que se pueden acelerar

- Modelos conceptuales y lógicos.
- Mapeos origen-destino.
- Sentencias `CREATE TABLE` y `CREATE VIEW`.
- Esquemas JSON y modelos tipados.
- Consultas de validación y pruebas de calidad.

---

## Artefactos adicionales

- Sugerencias de particionamiento y clustering.
- Inventarios de campos sensibles.
- Candidatos para etiquetas de políticas.
- Notas de migración y backfill.
- Checklists para pull requests.

---

## Estructura recomendada

```text
bigquery/
├── models/
│   ├── staging/
│   ├── intermediate/
│   └── marts/
├── mappings/
├── schemas/
└── tests/
```

- Versionar artefactos junto con el código.
- Mantener cada cambio acotado a un modelo o producto de datos.
- Respetar las convenciones existentes del repositorio.

---

## Flujo de trabajo

1. Definir el contrato de modelado.
2. Mapear los campos origen-destino.
3. Generar esquemas tipados.
4. Crear el modelo SQL.
5. Revisar diseño, seguridad y costos.
6. Validar y probar.
7. Aprobar mediante pull request.

---

## Contrato de modelado: información mínima

- Granularidad del modelo.
- Tablas origen y columnas relevantes.
- Campos obligatorios y opcionales.
- Convenciones de nombres.
- Claves y reglas de unicidad.
- Reglas para nulos y valores por defecto.

---

## Contrato de modelado: operación y gobierno

- Zona horaria y reglas para timestamps.
- Datos tardíos y estrategia de reprocesamiento.
- Particionamiento y clustering.
- Clasificación de PII y datos confidenciales.
- Consumidores y objetivo de frescura.
- Preguntas abiertas y responsables.

---

## Seguridad durante el prompting

- Usar placeholders para proyectos y datasets.
- Nunca pegar secretos, tokens o cadenas de conexión.
- Evitar registros productivos innecesarios.
- Redactar datos personales antes de compartir contexto.
- Usar variables de entorno o secretos administrados.

---

## Prompt: contrato de modelado

```text
Actúa como ingeniero senior de analítica. Crea un contrato de modelado
para una dimensión de clientes en BigQuery.

Granularidad: una fila por cliente maestro.
Fuentes: raw.crm_customer, raw.core_party.
Resultados: customer_key, source_system, created_at, updated_at, status.
Reglas: usar timestamps UTC, preservar identificadores origen,
no inventar mapeos, identificar ambigüedades como PREGUNTAS ABIERTAS
y clasificar campos sensibles.

Devuelve granularidad, claves, supuestos, definiciones,
preguntas pendientes y pruebas propuestas.
```

---

## Mapeo origen-destino

Solicitar el mapeo antes del SQL para separar decisiones de negocio de implementación.

Cada registro debe incluir:

- Tabla y campo origen.
- Modelo y campo destino.
- Transformación.
- Tipo y modo de BigQuery.
- Nulabilidad.

---

## Mapeo: trazabilidad y calidad

Cada registro también debe incluir:

- Regla de calidad.
- Clasificación de sensibilidad.
- Nivel de confianza.
- Estado del linaje.
- Responsable o pregunta pendiente.

La trazabilidad facilita revisión, mantenimiento y análisis de impacto.

---

## Ejemplo de mapeo

```yaml
- target: customer_key
  type: STRING
  mode: REQUIRED
  source: [raw.crm_customer.customer_id, raw.core_party.party_id]
  transformation: "Normalizar según la regla aprobada."
  lineage_status: confirmed
  sensitivity: internal
  quality_rules:
    - not_null
    - unique
```

**Regla:** Copilot no debe inventar la resolución de identidades.

---

## Esquema tipado

Generar dos artefactos sincronizados:

1. Esquema JSON para `bq` o la API de BigQuery.
2. Modelo de consumo en TypeScript o C#.

Requisitos:

- Conservar nombres, descripciones y modos.
- Representar campos anulables explícitamente.
- Conservar estructuras anidadas.
- Usar timestamps UTC.
- Reportar conversiones con pérdida de información.

---

## Tipos de BigQuery

`STRING` · `INT64` · `NUMERIC` · `BOOL` · `DATE`

`DATETIME` · `TIMESTAMP` · `GEOGRAPHY` · `JSON`

`ARRAY` · `STRUCT`

Validar los tipos con la [referencia oficial de tipos de datos de BigQuery](https://cloud.google.com/bigquery/docs/reference/standard-sql/data-types).

---

## Modelo tipado para consumidores

```typescript
export interface Customer {
  customer_key: string;
  created_at: string;
  preferences?: {
    language?: string;
  };
}
```

Para C#:

- Usar tipos de referencia anulables.
- Usar `DateTimeOffset` para instantes UTC.
- Incluir documentación XML en miembros públicos.
- Mantener credenciales fuera del código.

---

## Modelo SQL: reglas principales

- Usar BigQuery GoogleSQL.
- Mantener la granularidad declarada.
- Usar columnas explícitas; nunca `SELECT *`.
- Conservar la trazabilidad en comentarios SQL.
- Aplicar conversiones explícitas.

---

## Modelo SQL: comportamiento y costos

- Usar `SAFE_CAST` solo si el mapeo lo autoriza.
- Normalizar timestamps a UTC.
- Resolver duplicados y nulos de forma determinista.
- Justificar particionamiento y clustering.
- Evitar identificadores específicos de proyectos.

---

## Modelos incrementales

Definir antes de generar:

- Marca de agua o watermark.
- Estrategia de actualización.
- Clave de deduplicación.
- Ventana de reprocesamiento.
- Política para datos tardíos.
- Backfill y replay.

Si falta una decisión, generar una **PREGUNTA ABIERTA**.

---

## Revisión del diseño

Evaluar por separado de la generación de código:

- Granularidad y claves.
- Dimensiones estables, cambiantes o historizadas.
- Medidas aditivas, semi-aditivas o no aditivas.
- Cardinalidad de joins y riesgo de fan-out.
- Compatibilidad con consumidores tipados.

---

## Revisión de costos y operación

- Particionamiento y clustering.
- Volumen de datos escaneados.
- Frescura y latencia.
- Backfill y replay.
- Zonas horarias y calendarios.
- Patrones de consulta.

No solicitar un modelo “óptimo” sin proporcionar estos datos.

---

## Ciclo de validación

1. Generar un artefacto por solicitud.
2. Inspeccionar nombres, tipos, nulos y linaje.
3. Validar sintaxis y dry run fuera de producción.
4. Probar calidad y relaciones.
5. Comparar SQL, esquema y mapeo.
6. Resolver preguntas abiertas.
7. Desplegar mediante pull request.

---

## Prompts de revisión

```text
Compara el SQL, el esquema JSON y el mapeo YAML.
Lista cada diferencia por campo. No modifiques archivos.
```

```text
Revisa pérdida silenciosa de datos, nulos inesperados,
reducción de tipos, fan-out, claves duplicadas y errores de zona horaria.
Indica la columna y recomienda una prueba para cada problema.
```

---

## Prompt de validación segura

```text
Genera únicamente consultas de validación para BigQuery.
No generes SQL destructivo, DDL, credenciales ni comandos de producción.
```

---

## Ejercicio: contrato de modelado

### Objetivo

Definir el contrato para `customer` sin escribir SQL.

### Datos ficticios

- `raw.crm_customer.customer_id`
- `raw.crm_customer.email`
- `raw.crm_customer.status`
- `raw.crm_customer.created_at`
- `raw.core_party.party_id`
- `raw.core_party.country_code`

### Tiempo: 10 minutos

---

## Ejercicio: contrato de modelado — actividad

1. Identificar granularidad y clave.
2. Clasificar campos obligatorios y opcionales.
3. Documentar zona horaria, unicidad y nulos.
4. Marcar tres **PREGUNTAS ABIERTAS**.
5. Pedir a Copilot el contrato.

**Entregable:** `customer-contract.md`.

**Éxito:** declara granularidad, claves, tipos, seguridad y preguntas pendientes.

---

## Ejercicio: mapeo origen-destino

### Actividad — 12 minutos

1. Crear `mappings/customer.yml`.
2. Mapear `customer_key`, `email`, `status`, `created_at` y `country_code`.
3. Añadir transformación, tipo, modo y nulabilidad.
4. Añadir reglas de calidad y sensibilidad.
5. Marcar inferencias como `inferred`.

**Entregable:** YAML válido y preguntas abiertas.

---

## Ejercicio: prompt de mapeo

```text
Genera mappings/customer.yml a partir del contrato aprobado.
Conserva el linaje de cada campo, marca las inferencias,
no resuelvas conflictos silenciosamente y devuelve las preguntas abiertas.
```

**Éxito:** cada campo destino tiene origen, transformación, tipo, calidad y sensibilidad.

---

## Ejercicio: esquema y modelo tipado

### Actividad — 12 minutos

1. Generar `schemas/customer.json`.
2. Generar `schemas/customer.ts` o un modelo C#.
3. Representar campos anulables y timestamps.
4. Comparar nombres, tipos y nulabilidad.
5. Listar diferencias sin modificar archivos.

**Entregables:** esquema JSON y modelo tipado.

---

## Ejercicio: prompt de esquema

```text
Genera un esquema JSON de BigQuery y un modelo tipado a partir de
mappings/customer.yml. Conserva nombres, modos, descripciones y anidamiento.
Reporta cualquier conversión que pueda perder información.
```

**Éxito:** no existen discrepancias no justificadas entre los contratos.

---

## Ejercicio: modelo SQL y pruebas

### Actividad — 15 minutos

1. Generar `models/marts/dim_customer.sql`.
2. Prohibir `SELECT *`.
3. Resolver duplicados de forma determinista.
4. Generar pruebas de nulos, unicidad y valores aceptados.
5. Revisar fan-out, conversiones y zonas horarias.

**Entregables:** SQL y consultas de prueba.

---

## Ejercicio: prompt SQL

```text
Genera dim_customer.sql y pruebas de calidad a partir del mapeo y
esquema aprobados. Usa GoogleSQL, columnas explícitas,
conversiones declaradas y comportamiento determinista para duplicados.
No incluyas identificadores de proyecto ni comandos de despliegue.
```

**Éxito:** el SQL conserva la granularidad y cada regla importante tiene una validación.

---

## Ejercicio: seguridad y costos

### Actividad — 10 minutos

1. Clasificar `email` según la política local.
2. Proponer una etiqueta sin inventar el identificador del recurso.
3. Revisar la necesidad de seguridad a nivel de fila.
4. Evaluar particionamiento, clustering y volumen leído.
5. Redactar tres riesgos y una acción por riesgo.

**Entregable:** `customer-review.md`.

---

## Ejercicio: prompt de seguridad

```text
Revisa el modelo, mapeo y esquema para detectar riesgos de PII,
acceso a columnas, filtros de filas, costo de consulta,
particionamiento y clustering. No asignes etiquetas ni permisos reales.
Devuelve riesgos, evidencia, responsable y acción recomendada.
```

**Éxito:** cada riesgo incluye evidencia, propietario y decisión requerida.

---

## Ejercicio: reconciliación final

### Actividad — 10 minutos

1. Comparar contrato, YAML, JSON, SQL y pruebas.
2. Resolver o asignar preguntas abiertas.
3. Completar la plantilla de pull request.
4. Identificar qué generó Copilot y qué verificó una persona.
5. Decidir si el cambio está listo o bloqueado.

**Entregable:** matriz de reconciliación y resumen del pull request.

---

## Prompt de reconciliación

```text
Compara todos los artefactos del modelo customer y genera una matriz
con: campo, diferencia, impacto, evidencia, responsable y estado.
No modifiques archivos ni apruebes el despliegue.
```

**Éxito:** no quedan diferencias críticas ni decisiones de seguridad sin responsable.

---

## Seguridad y gobierno

### Revisión humana obligatoria

- Clasificación y etiquetas de políticas.
- Filtros de filas y principales autorizados.
- Enmascaramiento.
- Roles IAM y cuentas de servicio.
- Movimiento entre proyectos o regiones.
- Exposición de datos personales o confidenciales.

Nunca incluir secretos, cadenas de conexión o datos productivos innecesarios en prompts o commits.

---

## Controles de acceso en BigQuery

- Las etiquetas de políticas permiten control a nivel de columna.
- La seguridad a nivel de fila restringe subconjuntos de registros.
- Los controles deben alinearse con la clasificación aprobada.
- Los responsables de datos y seguridad deben aprobar las reglas.

Fuentes: [control a nivel de columna](https://cloud.google.com/bigquery/docs/column-level-security-intro) y [seguridad a nivel de fila](https://cloud.google.com/bigquery/docs/row-level-security-intro).

---

## Criterios de finalización

- La granularidad y las claves están documentadas.
- Cada campo tiene origen, transformación, tipo, nulabilidad y sensibilidad revisados.
- SQL, mapeo y esquema tipado son consistentes.
- Las ambigüedades están documentadas y asignadas.
- Existen pruebas de calidad y regresión.

---

## Criterios de finalización: seguridad y operación

- Sintaxis y dry run validados fuera de producción.
- Particionamiento y clustering justificados.
- Seguridad y acceso revisados por sus responsables.
- No hay secretos ni registros productivos innecesarios.
- El pull request identifica lo generado por Copilot y lo verificado por personas.

---

## Plantilla de pull request

```markdown
## Cambio de modelado BigQuery

### Artefactos
- Modelo:
- Mapeo:
- Esquema:
- Pruebas:

### Contrato
- Granularidad:
- Clave principal/de negocio:
- Estrategia de actualización:
- Particionamiento/clustering:
```

---

## Plantilla de pull request: validación

```markdown
### Validación
- [ ] Sintaxis o dry run fuera de producción
- [ ] SQL, esquema y mapeo comparados
- [ ] Pruebas de calidad actualizadas
- [ ] Campos sensibles y accesos revisados
- [ ] Sin secretos ni registros productivos
- [ ] Preguntas abiertas resueltas

### Uso de Copilot
Copilot creó borradores y verificaciones de consistencia.
Los revisores validaron reglas de negocio, linaje, tipos,
seguridad, pruebas y seguridad del despliegue.
```

---

## Mensajes clave

1. Definir antes de generar.
2. Mapear antes de implementar.
3. Mantener sincronizados SQL, esquemas y mapeos.
4. Validar fuera de producción.
5. Practicar con datos ficticios.
6. Mantener el control humano.

---

## Fuentes oficiales

- [Ingeniería de prompts para GitHub Copilot](https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering)
- [Personalizar Copilot para un proyecto](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-copilot-overview)
- [Tipos de datos de BigQuery](https://cloud.google.com/bigquery/docs/reference/standard-sql/data-types)
- [Control de acceso a nivel de columna en BigQuery](https://cloud.google.com/bigquery/docs/column-level-security-intro)
- [Seguridad a nivel de fila en BigQuery](https://cloud.google.com/bigquery/docs/row-level-security-intro)
