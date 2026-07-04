# VAERS: por qué los conteos crudos engañan

Análisis crítico de datos de farmacovigilancia usando el Sistema de Reporte de Eventos
Adversos de Vacunas (VAERS) de EE. UU.

## Pregunta del proyecto

> **¿Qué revela VAERS sobre la seguridad de las vacunas cuando se analiza con
> denominadores, y por qué los conteos crudos de reportes llevan a conclusiones falsas?**

La tesis no es "las vacunas son seguras" ni "son peligrosas" — es una tesis de **método**:
un mismo dato produce lecturas opuestas según se analice bien o mal. El proyecto demuestra
la diferencia.

## Por qué esta pregunta

VAERS es una de las fuentes de datos públicas peor interpretadas que existen. Es un sistema
de vigilancia **pasivo**: cualquiera puede enviar un reporte, los reportes no están
verificados y **no existe relación causa-efecto establecida** entre el evento reportado y la
vacuna (esto lo dice la propia FDA en la documentación del sistema). Además, los datos
públicos **no traen denominador**: no sabemos cuántas dosis se administraron, así que un
conteo de reportes por sí solo no significa nada.

El error clásico es tomar el número absoluto de reportes y presentarlo como evidencia de
daño. Este proyecto reconstruye ese error y luego lo corrige calculando **tasas por dosis
administrada**, que es la única forma de que el número tenga sentido.

## Metodología

1. **Cargar** los datos crudos de VAERS (tres archivos por año: reportes, vacunas, síntomas).
2. **Perfilar** la estructura, el grano de cada tabla y su calidad (nulos, encoding, duplicados).
3. **Aislar** los reportes asociados a vacunas COVID-19 (`VAX_TYPE == 'COVID19'`).
4. **Mostrar el conteo crudo** — la lectura ingenua.
5. **Traer el denominador**: dosis COVID-19 administradas en EE. UU. (Our World in Data).
6. **Calcular tasas** de reportes por millón de dosis y comparar la lectura cruda vs. la
   lectura con denominador.
7. **Documentar las limitaciones** en cada paso (ver sección siguiente).

## Limitaciones (leer antes de interpretar cualquier resultado)

Estas limitaciones no son un descargo legal: son parte del análisis y condicionan toda
conclusión.

- **Sistema pasivo y voluntario.** Los reportes los envía cualquiera (pacientes, familiares,
  personal de salud, fabricantes). No son un muestreo representativo.
- **Sin causalidad.** Un reporte solo significa que un evento ocurrió *después* de la
  vacunación, no *por causa* de ella. Correlación temporal ≠ causa.
- **Subregistro.** VAERS capta solo una fracción de los eventos reales, y esa fracción es
  desconocida y variable.
- **Sin denominador en la fuente.** El denominador (dosis administradas) hay que traerlo de
  otra fuente, y su cobertura temporal/geográfica no calza perfecto con los reportes.
- **Sesgo de reporte.** La atención mediática y las campañas de vacunación masivas inflan el
  número de reportes de forma independiente al riesgo real (sesgo de estímulo).
- **Cambio en los datos públicos (8 de mayo de 2025).** Desde esa fecha los datasets públicos
  incluyen reportes secundarios del mismo caso. Si no se controla, **infla los conteos**
  artificialmente. Hay que deduplicar por `VAERS_ID`.
- **Encoding.** Los CSV de VAERS están en Latin-1 (ISO-8859-1), no UTF-8. Cargarlos con el
  encoding por defecto de pandas produce error.

## Estructura del repositorio

```
proyecto-vaers/
├── README.md              ← este archivo
├── DATA_SOURCES.md        ← procedencia, licencia y limitaciones de cada fuente
├── requirements.txt
├── .gitignore
├── data/
│   ├── raw/               ← datos originales (NO se editan, NO se commitean si pesan)
│   └── processed/         ← datos limpios generados por el código
├── notebooks/
│   └── 01_vaers_exploracion.ipynb   ← carga y primer perfilado
└── src/                   ← funciones reutilizables (más adelante)
```

**Regla de oro:** `data/raw/` es inmutable. Todo lo demás se regenera corriendo el código.

## Reproducir el análisis

1. Abre `notebooks/01_vaers_exploracion.ipynb` en Google Colab.
2. Descarga los datos de VAERS desde https://vaers.hhs.gov/data/datasets.html (hay que
   aceptar los términos), y súbelos a Colab siguiendo las instrucciones del notebook.
3. El denominador (dosis de OWID) se descarga solo por código.

## Estado

🚧 En construcción — Fase 1: exploración y perfilado de datos.
