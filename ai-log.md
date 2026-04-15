# AI Log — Equipo GreenByte
**HackODS UNAM 2026 · ODS 13**

---

## Herramientas utilizadas

- Claude (claude.ai) — Anthropic
- GitHub Copilot (VS Code)

---

## Filosofía de uso
En GreenByte decidimos utilizar la IA exclusivamente como un **acelerador de desarrollo técnico y asistente de depuración**. Delegamos en estas herramientas la generación de código mecánico (boilerplate para iteraciones y configuraciones de visualización) y la resolución de conflictos de dependencias o layout en Quarto. 

Sin embargo, el núcleo de nuestro proyecto se mantuvo estrictamente humano. La selección de las variables climáticas (SST, NDVI, EVI, anomalías de temperatura y precipitación), la estrategia de calcular deltas (Δ) interanuales para medir el impacto, y toda la narrativa explicativa que conecta los datos con los Objetivos de Desarrollo Sostenible fueron decisiones 100% analíticas tomadas por el equipo. La IA no pensó por nosotros, solo nos ayudó a teclear y renderizar más rápido.
**Criterio de delegación:** Si la decisión requiere conocimiento del contexto climático mexicano o criterio analítico sobre los ODS, la tomamos nosotros. Si la tarea es mecánica o de sintaxis, la delegamos con revisión.

---

## Registro de uso

### 2026-04-06 | Claude | Estructura del repositorio
- **Tarea:** Generar la estructura base del repositorio de GitHub conforme a los requisitos del Módulo A de la rúbrica HackODS (README, LICENSE CC BY-SA, ai-log.md, carpetas datos/scripts/dashboard).
- **Prompt:** Análisis de los notebooks de extracción y EDA para extraer fuentes, variables y metadatos reales del proyecto.
- **Resultado:** README.md con tabla de metadatos completa, LICENSE CC BY-SA 4.0, ai-log.md con plantilla oficial, estructura de carpetas.
- **Decisión del equipo:** Aceptamos la estructura base. Revisamos y validamos que todos los metadatos (fuentes, fechas, licencias, variables) corresponden exactamente al pipeline real del proyecto. La pregunta central y la descripción narrativa fueron escritas por el equipo; Claude organizó el formato.

---

- Gemini 3.1 Pro (Asistente de programación y depuración de código)
- GitHub Copilot (Autocompletado de sintaxis en el IDE)

## Registro de uso

### 2026-04-12 | Gemini | Procesamiento de Datos y Deltas
- **Tarea**: Teníamos un dataset histórico y necesitábamos crear un pipeline en Pandas para separar el año base (referencia) del año final, unirlos por coordenadas (lat, lon) y calcular las diferencias para múltiples variables climáticas a la vez.
- **Prompt**: "Tengo un DataFrame de pandas con datos climáticos por año, latitud y longitud. Necesito un script eficiente para filtrar el año mínimo y máximo, hacer un merge espacial y calcular la diferencia iterando sobre una lista de variables."
- **Resultado**: La IA sugirió usar `df.merge()` con sufijos `_ref` y `_fin`, seguido de un bucle `for` para crear las nuevas columnas dinámicamente.
- **Decisión / Modificación**: Implementamos la solución base porque el merge espacial era óptimo. Sin embargo, modificamos el código resultante para agregar validaciones previas (`[v for v in VARS_ANOM if v in df_full.columns]`) asegurando que el pipeline no se rompiera si en futuras versiones del CSV faltaban variables. Esto dio origen a nuestra versión final de datos `master_greenbyte_v4.csv`.

### 2026-04-13 | Copilot | Boilerplate de Visualización (Plotly)
- **Tarea**: Generar la estructura repetitiva para la configuración inicial de los mapas base usando Plotly y WebGL por el volumen de datos.
- **Resultado**: Copilot sugirió la estructura del diccionario `go.Scattergl` dentro de nuestro bucle principal de variables.
- **Decisión / Modificación**: Aceptamos el autocompletado para la sintaxis básica (`x`, `y`, `mode='markers'`). No obstante, la IA sugería colores estáticos. Nosotros intervenimos para diseñar un diccionario de configuración de ejes de color (`coloraxis_cfg`) dinámico. Además, personalizamos el `hovertemplate` desde cero usando variables locales y etiquetas en español para asegurar una experiencia de usuario clara y alineada a nuestra narrativa.

### 2026-04-14 | Gemini | Matemáticas de Layout y Renderizado en Quarto
- **Tarea**: Al integrar los gráficos en nuestro dashboard de Quarto con la directiva `## Row {height="800px"}`, las gráficas se renderizaban completamente comprimidas, las barras de color se superponían y el texto era ilegible.
- **Prompt**: "Ayúdame a corregir unas gráficas de un tablero de quarto, se ve todo comprimido. Te comparto el código del make_subplots donde itero 7 variables..."
- **Resultado**: La IA diagnosticó que el cálculo manual que teníamos para la posición `y` de las barras de color (`y=0.97 - (row - 1) * 0.52`) generaba coordenadas negativas en la tercera fila, rompiendo la cuadrícula de Plotly. Nos propuso un bloque de código refactorizado usando variables relativas (`row_height`, `col_width`) y un cálculo de altura de contenedor dinámico basado en la cantidad de filas.
- **Decisión / Modificación**: Aceptamos la refactorización matemática porque solucionaba un problema puro de renderizado del motor de Plotly. Ajustamos los márgenes propuestos por la IA (`margin=dict(t=70, b=30, l=40, r=40)`) basándonos en pruebas empíricas en nuestro monitor para que las leyendas respiraran mejor, y actualizamos el contenedor superior en el archivo `.qmd` a una altura dinámica.

### 2026-04-14 | Gemini | Tratamiento de Valores Atípicos (Outliers)
- **Tarea**: En nuestros mapas de anomalías (especialmente en precipitación), los valores extremos estaban arruinando la escala de color, haciendo que el 90% del mapa se viera gris y ocultando las tendencias reales.
- **Prompt**: "¿Cuál es la mejor manera en pandas y plotly para limitar el efecto de valores atípicos extremos en una escala de color divergente sin borrar los datos del dataset?"
- **Resultado**: Sugirió usar los límites `cmin` y `cmax` de Plotly basándose en cuantiles estadísticos en lugar del valor máximo absoluto, proponiendo el cuantil `0.95`.
- **Decisión / Modificación**: Implementamos la lógica matemática sugerida: `vmax = df_anom[col].abs().quantile(0.97)`. Elegimos el cuantil `0.97` (y no el 0.95 sugerido por la IA) después de hacer análisis exploratorio iterativo en nuestra libreta (`GreenByte_Historia_Final.ipynb`), ya que el percentil 97 preservaba mejor la intensidad de los eventos de sequía extrema que queríamos evidenciar en nuestra historia.

### NO usamos IA para hacer lo siguiente:
- **La curaduría de la historia:** El cruce entre el ODS 13 (Acción por el Clima) y nuestros datos, así como la conclusión sobre los días de calor extremo (`lst_extreme_days`).
- **Elección de paletas semánticas:** La elección de `RdBu_r` (Rojo-Azul invertido) fue una decisión consciente del equipo para representar el calor con rojos y el frío/humedad con azules, corrigiendo los defaults de las herramientas.
- **Validación del contexto local:** La interpretación geográfica de las zonas más afectadas mostradas en el mapa.
