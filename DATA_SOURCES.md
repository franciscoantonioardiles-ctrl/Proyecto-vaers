# Fuentes de datos

Documentación de procedencia. Cada fuente incluye su acceso, licencia, frecuencia de
actualización y — lo más importante para este proyecto — sus limitaciones metodológicas.

---

## 1. VAERS — Vaccine Adverse Event Reporting System (fuente principal)

| Campo | Detalle |
|---|---|
| **Organismo** | CDC + FDA (EE. UU.) |
| **URL** | https://vaers.hhs.gov/data/datasets.html |
| **Contenido** | Reportes de eventos adversos posteriores a vacunación, 1990–presente |
| **Formato** | 3 CSV por año: `{año}VAERSDATA.csv`, `{año}VAERSVAX.csv`, `{año}VAERSSYMPTOMS.csv` |
| **Acceso** | Público, pero requiere aceptar términos antes de descargar (descarga manual) |
| **Encoding** | **Latin-1 / ISO-8859-1** (no UTF-8) |
| **Licencia** | Dominio público (obra del gobierno federal de EE. UU.) |
| **Actualización** | Semanal aprox. |

**Grano de cada archivo (crítico para los joins):**
- `VAERSDATA`: **una fila por reporte** (clave: `VAERS_ID`). ~35 columnas.
- `VAERSVAX`: **una fila por vacuna** — un reporte puede tener varias (relación 1:N).
- `VAERSSYMPTOMS`: hasta 5 síntomas por fila, y puede haber varias filas por reporte.

**Columnas clave:** `VAERS_ID`, `RECVDATE`, `STATE`, `AGE_YRS`, `SEX`, `DIED`, `DATEDIED`,
`L_THREAT`, `HOSPITAL`, `DISABLE`, `VAX_DATE`, `ONSET_DATE`, `SYMPTOM_TEXT` (en DATA);
`VAX_TYPE`, `VAX_MANU`, `VAX_NAME`, `VAX_DOSE_SERIES` (en VAX).

**Limitaciones (fuente: documentación de FDA/CDC):**
- Sistema **pasivo y voluntario** → no es muestra representativa.
- Reportes **no verificados**, **sin causalidad** establecida.
- **Subregistro** de magnitud desconocida.
- **Sin denominador**: no incluye dosis administradas.
- Desde el **8 de mayo de 2025** los datos públicos incluyen reportes secundarios →
  deduplicar por `VAERS_ID` o los conteos se inflan.

---

## 2. Our World in Data — Vacunación COVID-19 (denominador)

| Campo | Detalle |
|---|---|
| **Organismo** | Our World in Data (Universidad de Oxford / Global Change Data Lab) |
| **URL** | https://github.com/owid/covid-19-data (carpeta `public/data/vaccinations`) |
| **Contenido** | Dosis administradas por país y fecha (incluye EE. UU.) |
| **Formato** | CSV, cargable directo con `pd.read_csv(url_raw)` |
| **Acceso** | Público, abierto, sin registro |
| **Licencia** | Creative Commons BY 4.0 |
| **Actualización** | Compilado de reportes oficiales (histórico) |

**Rol en el proyecto:** aporta el **denominador** (dosis administradas en EE. UU.) que VAERS
no trae, para convertir conteos crudos en **tasas por millón de dosis**.

**Limitaciones:**
- La cobertura temporal del denominador no calza exactamente con la de los reportes.
- Los datos de dosis provienen de reportes oficiales, sujetos a sus propias revisiones.

---

## Alternativas de denominador (opcional)

- **CDC COVID Data Tracker / data.cdc.gov** — dosis administradas en EE. UU. vía API Socrata.
  Más granular por estado, útil si se quiere cruzar con la columna `STATE` de VAERS.

## Nota sobre vigilancia activa (contexto metodológico)

VAERS es vigilancia **pasiva**. Los sistemas **activos** — Vaccine Safety Datalink (VSD) y
FDA BEST — son metodológicamente más rigurosos porque tienen cohortes con denominador y
grupos de comparación. No son de descarga pública abierta, pero conviene citarlos en el
análisis para situar correctamente el alcance de lo que VAERS puede y no puede demostrar.
