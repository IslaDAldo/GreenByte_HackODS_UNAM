# 🌱 GreenByte — HackODS UNAM 2026

**ODS 13 · Acción por el Clima** 

> **Equipo:** GreenByte
> **Integrantes:** Aldo Heraclio de la Isla · Mayra Cuellar Urbano · Juan Mario Sosa
> **Hackatón:** HackODS UNAM 2026 — primer hackatón de la UNAM para convertir datos abiertos en narrativas visuales sobre los ODS

---

## Pregunta central

**¿Y si la vegetación mexicana no muere por que la cortan, sino por lo que le niegan — el agua? ¿Y si la huella humana no destruye el ecosistema directamente, sino que lo calienta y lo seca hasta que el clima hace el trabajo final?**

Para responder, antes debemos comprender como funciona el clima en México **¿Qué variables climáticas y humanas dominan la salud del ecosistema mexicano, en qué medida, y dónde se concentra el daño cuando actúan simultáneamente?**

No partimos de una hipótesis prefabricada. Interrogamos el territorio como una escena del crimen ambiental: cruzamos variables atmosféricas (NO₂, CO, SO₂), biológicas (NDVI, EVI) e hidrológicas (precipitación, anomalías CHIRPS) para descubrir no solo *si* el país cambia, sino *de qué forma* está fallando la respuesta natural ante el estrés climático y humano.

---

## Descripción del proyecto

GreenByte construye un tablero de datos abiertos satelitales para México (2019–2024) con cuatro capas analíticas:

1. **Escaneo de tendencias nacionales** — Mann-Kendall + pendiente de Sen para variables climáticas y ambientales. ¿Qué está cambiando y en qué dirección?
2. **Atribución de la salud vegetal** — Random Forest (n=35,461 obs., R²=0.895) + regresión multivariada para cuantificar cuánto explica cada variable sobre el NDVI.
3. **Dos motores climáticos** — Separación Pacífico/Atlántico con descomposición ENSO. México tiene dos regímenes climáticos independientes con tendencias opuestas en SST residual.
4. **Hotspots de multi-estrés** — Índice compuesto (z_LST + z_NO₂ + z_T2m + z_Extremos − z_NDVI) con autocorrelación espacial Gi* de Getis-Ord (999 permutaciones, k=15 KNN). Identifica dónde colapsan simultáneamente múltiples estresores.

---

## Hallazgos principales

El análisis sobre una malla de ~25 km para el período 2019–2024 revela:

- **La precipitación domina la salud vegetal con 78% de importancia relativa** (Random Forest, n=35,461, R²=0.895). LST_day contribuye 6.4%, gHM 4.7%, NO₂ 4.5%. El ecosistema mexicano es fundamentalmente hídrico: no muere de fiebre, muere de sed.

- **México tiene dos motores climáticos independientes.** La SST residual (limpia del efecto ENSO) muestra tendencias opuestas: el Atlántico/Golfo se calienta (+0.419 °C/año, Sen's slope) y el Pacífico se enfría (−0.792 °C/año). Esto implica que una fracción de la variabilidad climática observada es de origen natural — distinción crítica para priorizar intervenciones de política.

- **El impacto humano actúa principalmente a través del clima, no directamente.** El análisis de mediación causal (Baron & Kenny, bootstrap IC95%, 1000 iteraciones) muestra que el SO₂ ejerce el 204% de su efecto sobre el NDVI de forma indirecta vía temperatura superficial, y el gHM ejerce el 91% vía precipitación. Los factores humanos amplifican el estrés climático en lugar de dañar la vegetación de forma independiente.

- **Los puntos de crisis son identificables y localizables.** El análisis Gi* detecta píxeles de hotspot con significancia espacial formal (z > 1.96, p < 0.05) concentrados en el centro del país y la Zona Metropolitana del Valle de México — zonas donde presión humana, calor extremo y degradación vegetal colapsan simultáneamente en el snapshot de 2023.

### Limitaciones explícitas

Este es un primer escaneo diagnóstico, no un dictamen. Las limitaciones más importantes son:

- **Ventana temporal corta:** 6 años para gases (S5P TROPOMI desde 2019), 10 años para otras variables. Las tendencias deben interpretarse con cautela.
- **Hotspots acotados a 2023:** año de El Niño, lo que puede inflar el estrés térmico observado respecto a años neutros.
- **Autocorrelación espacial no corregida formalmente** en las regresiones multivariadas — los errores estándar pueden estar subestimados.
- **Discontinuidad instrumental en gases:** OMI/Aura (2015–2018) y S5P TROPOMI (2019–2024) no son comparables en valor absoluto.

---

## Para quién es este análisis

Este tablero está dirigido principalmente a **tomadores de decisión en política ambiental** — equipos de CONABIO, SEMARNAT y gobiernos estatales — como herramienta de priorización espacial: una primera aproximación que reduce el espacio de búsqueda e indica *dónde mirar primero* antes de invertir en monitoreo de mayor resolución o intervenciones de restauración.

También tiene valor divulgativo para audiencias generales interesadas en entender cómo interactúan clima, vegetación y presión humana en el territorio mexicano.

Lo que este análisis **no pretende hacer:** prescribir política, establecer causalidad definitiva, ni reemplazar estudios de mayor profundidad temporal o resolución espacial.

---

## Justificación de la selección de datos

La pregunta central exige medir tres dimensiones simultáneamente: estado del ecosistema (NDVI, LST), presión climática (precipitación, temperatura del aire, ENSO) y presión humana (gases, modificación del hábitat, deforestación). Ningún dataset individual cubre las tres para México a escala nacional con resolución suficiente para análisis espacial local.

Las decisiones críticas de selección fueron:

- **CHIRPS sobre ERA5 para precipitación:** CHIRPS fusiona observaciones satelitales con pluviómetros terrestres, produciendo estimaciones más precisas en zonas montañosas (Sierra Madre Occidental y Oriental). ERA5-Land se usa para temperatura del aire (T2m), donde es el estándar global con referencia histórica 1981–2010.

- **MODIS sobre Landsat para NDVI/LST:** La cobertura temporal continua 2015–2024 y la frecuencia de revisita (16 días MOD13Q1, 8 días MOD11A2) superan a Landsat para series de tiempo largas a escala nacional. Landsat tiene mayor resolución espacial pero mayor porcentaje de nubosidad y cobertura irregular.

- **Sentinel-5P TROPOMI sobre OMI/Aura para gases:** La resolución de TROPOMI (~3.5×5.5 km) frente a OMI (~13×24 km) es determinante para detectar gradientes urbano-rurales de NO₂ a escala de hotspot. La discontinuidad instrumental se documenta y limita el análisis a la ventana 2019–2024.

- **Hansen GFC v1.11 para deforestación:** Único dataset de pérdida forestal anual con cobertura global, resolución de 30 m y serie histórica desde 2000 validada internacionalmente. Permite conectar deforestación acumulada con los hotspots climáticos.

- **ONI (NOAA CPC) como covariable ENSO:** Sin controlar por el ciclo ENSO, las tendencias de SST absorben señal climática natural que no es tendencia antrópica. La descomposición SST_observada − SST_ENSO es lo que permite identificar los dos motores climáticos independientes.

---
##  Dataset maestro — `master_greenbyte_v4.csv`

Resolución espacial: malla ~25 km sobre México (bbox: −118.5, 14.5, −86.5, 32.8)  
Período completo: 2015–2024 | Ventana analítica armonizada: 2019–2024

### Variables, fuentes, fechas de acceso y licencias

| Variable | Fuente | Asset / URL de acceso | Fecha de descarga | Licencia |
|----------|--------|----------------------|-------------------|-----------|
| `year`, `longitude`, `latitude` | Malla 25 km | — | — | — |
| `ONI` | NOAA CPC | [oni.ascii.txt](https://www.cpc.ncep.noaa.gov/data/indices/oni.ascii.txt) | Abril 2026 | Dominio público |
| `sst` | NOAA OISST V2.1 | `NOAA/CDR/OISST/V2_1` (GEE) | Abril 2026 | Dominio público |
| `chlor_a` | NASA MODIS-Aqua L3 | `NASA/OCEANDATA/MODIS-Aqua/L3SMI` (GEE) | Abril 2026 | Dominio público |
| `t2m_anomaly` | ERA5-Land ECMWF vs. ref. 1981–2010 | `ECMWF/ERA5_LAND/MONTHLY_AGGR` (GEE) | Abril 2026 | Copernicus Open Access |
| `loss_anual`, `loss_acum`, `treecover2000` | Hansen GFC v1.11 | `UMD/hansen/global_forest_change_2023_v1_11` (GEE) | Abril 2026 | CC BY 4.0 |
| `gHM` | Global Human Modification Index | `CSP/HM/GlobalHumanModification` (GEE) | Abril 2026 | CC0 (dominio público) |
| `fire_count` | NASA FIRMS VIIRS-SNPP | `NASA/FIRMS/VIIRS_SNPP_NRT` (GEE) | Abril 2026 | Dominio público |
| `NDVI`, `EVI` | MODIS MOD13Q1 v061 | `MODIS/061/MOD13Q1` (GEE) | Abril 2026 | Dominio público |
| `LST_day` | MODIS MOD11A2 v061 | `MODIS/061/MOD11A2` (GEE) | Abril 2026 | Dominio público |
| `lst_extreme_days` | MODIS MOD11A2 vs. percentil 95 | `MODIS/061/MOD11A2` (GEE) | Abril 2026 | Dominio público |
| `ET` | MODIS MOD16A2 v061 | `MODIS/061/MOD16A2` (GEE) | Abril 2026 | Dominio público |
| `precipitation` | CHIRPS pentadal v2.0 | `UCSB-CHG/CHIRPS/PENTAD` (GEE) | Abril 2026 | CC0 (dominio público) |
| `precip_anomaly` | CHIRPS vs. climatológico 1981–2010 | `UCSB-CHG/CHIRPS/PENTAD` (GEE) | Abril 2026 | CC0 (dominio público) |
| `precip_extreme_days` | CHIRPS vs. percentil 95 pentadal | `UCSB-CHG/CHIRPS/PENTAD` (GEE) | Abril 2026 | CC0 (dominio público) |
| `NO2`, `CO`, `SO2` | Sentinel-5P TROPOMI | `COPERNICUS/S5P/OFFL/L3_*` (GEE) | Abril 2026 | Copernicus Open Access |


> ⚠️ **Nota de discontinuidad instrumental:** Las series de gases de 2015–2018 (OMI/Aura) y 2019–2024 (S5P TROPOMI) **no son comparables en valor absoluto**. Todo análisis que incluya gases usa la ventana armonizada 2019–2024. Ver `scripts/Extraccion_Variables_Ambientales_Mexico.ipynb` para el detalle técnico del fallback instrumental.

### Fuentes oficiales

| Dataset | URL de acceso oficial |
|---------|----------------------|
| Hansen GFC v1.11 | https://earthenginepartners.appspot.com/science-2013-global-forest |
| ERA5-Land (ECMWF) | https://cds.climate.copernicus.eu/datasets/reanalysis-era5-land-monthly-means |
| MODIS MOD13Q1, MOD11A2, MOD16A2 | https://lpdaac.usgs.gov |
| CHIRPS v2.0 | https://www.chc.ucsb.edu/data/chirps |
| NOAA OISST V2.1 | https://www.ncei.noaa.gov/products/optimum-interpolation-sst |
| NASA FIRMS VIIRS-SNPP | https://firms.modaps.eosdis.nasa.gov |
| Sentinel-5P TROPOMI | https://sentinels.copernicus.eu/web/sentinel/missions/sentinel-5p |
| NOAA CPC ONI | https://www.cpc.ncep.noaa.gov/data/indices/oni.ascii.txt |
| Global Human Modification | https://datadryad.org/stash/dataset/doi:10.5061/dryad.zs7h44j |

### Licencias

| Dataset | Versión | Fecha de acceso | Licencia |
|---------|---------|-----------------|----------|
| Hansen GFC | v1.11 | Abril 2026 | CC BY 4.0 |
| ERA5-Land | Monthly aggregated | Abril 2026 | Copernicus Open Access |
| MODIS (MOD13Q1, MOD11A2, MOD16A2) | v061 | Abril 2026 | NASA Open Data (dominio público) |
| CHIRPS | Pentadal v2.0 | Abril 2026 | CC0 (dominio público) |
| NOAA OISST | V2.1 CDR | Abril 2026 | NOAA Open Data (dominio público) |
| NASA FIRMS VIIRS | SNPP NRT | Abril 2026 | NASA Open Data (dominio público) |
| Sentinel-5P TROPOMI | OFFL L3 | Abril 2026 | Copernicus Open Access |
| NOAA CPC ONI | — | Abril 2026 | NOAA Public Domain |
| Global Human Modification | v1 | Abril 2026 | CC0 (dominio público) |

Todos los datasets son de acceso público y gratuito, sin restricción de uso académico. Los datos NASA son de dominio público por política federal estadounidense. Los datos Copernicus son de acceso abierto bajo la [Política de Datos Copernicus](https://www.copernicus.eu/en/access-data/copyright-and-licences). Todos los datos se accedieron vía Google Earth Engine (proyecto: `greenbyte-hackods-unam-2026`).

---

## Estructura del repositorio

```
GreenByte_HackODS_UNAM/
├── README.md                        ← Este archivo
├── LICENSE                          ← CC BY-SA 4.0
├── ai-log.md                        ← Declaratoria de uso de IA (plantilla oficial HackODS)
├── datos/
│   ├── README.md                    ← Descripción de variables y estructura de archivos
│   ├── datos_crudos/                ← CSVs exportados desde GEE (greenbyte_A/B1/B2/C_YYYY.csv)
│   └── datos_procesados/            ← Dataset maestro y derivados
│       ├── master_greenbyte_v4.csv                ← Dataset completo 2015–2024
│       ├── master_greenbyte_v4_2019_2024.csv      ← Ventana analítica armonizada
│       ├── master_greenbyte_v4_hotspots.csv       ← Dataset con scores Gi* y flag hotspot
│       ├── sst_descomposicion_enso.csv            ← SST observada + residual ENSO
│       ├── tendencias_nacionales_v3.csv           ← Pendientes Mann-Kendall por variable
│       └── mann_kendall_tendencias_v3.csv         ← Test completo (tau, p-value, Sen's slope)
├── scripts/
│   ├── Extraccion_Variables_Ambientales_Mexico.ipynb  ← Pipeline de extracción GEE v4
│   └── Analisis_EDA.ipynb                             ← Análisis, atribución y hotspots v3
└── dashboard/
    ├── index.qmd                    ← Tablero Quarto principal
    ├── greenbyte.css                ← Estilos del tablero
    └── imagenes/                    ← Recursos visuales del tablero
```

---

## Cómo reproducir el pipeline

### Requisitos

- Cuenta activa de Google Earth Engine con proyecto: `greenbyte-hackods-unam-2026`
- Python 3.10+ con: `earthengine-api`, `geemap`, `pandas`, `numpy`, `matplotlib`, `statsmodels`, `scikit-learn`, `esda`, `libpysal`, `pymannkendall`, `seaborn`, `folium`, `scipy`
- Para el tablero: Quarto 1.4+ con kernel Python, o `uv` con el `pyproject.toml` incluido

### Pasos

1. **Extracción GEE** — Ejecutar `scripts/Extraccion_Variables_Ambientales_Mexico.ipynb`. Lanza 40 tareas en paralelo en GEE (A + B1 + B2 + C × 10 años). Tiempo estimado: ~4 horas. Los CSVs van a `datos/datos_crudos/`.

2. **Consolidación** — Ejecutar la sección "Consolidación — Dataset Maestro v4" del mismo notebook. Genera `master_greenbyte_v4.csv` y `master_greenbyte_v4_2019_2024.csv` en `datos/datos_procesados/`.

3. **Análisis** — Ejecutar `scripts/Analisis_EDA.ipynb`. Carga `master_greenbyte_v4_2019_2024.csv` como dataset principal y produce todas las visualizaciones, correlaciones, hotspots y exportaciones derivadas.

4. **Tablero** — Desde `dashboard/`, ejecutar `quarto preview index.qmd` (o `uv run quarto preview index.qmd` con el entorno virtual del proyecto).

> **Nota:** Si no se desea re-ejecutar la extracción GEE, los datasets procesados están disponibles en `datos/datos_procesados/` y el análisis puede iniciarse desde el paso 3.

---

## Licencia

Este proyecto se distribuye bajo **CC BY-SA 4.0**. Ver archivo `LICENSE`.

Los datasets de terceros conservan sus licencias originales (ver tabla de metadatos arriba). Los datos NASA son de dominio público por la [NASA Open Data Policy](https://www.nasa.gov/open/nasa-open-data-policy/).
