# Análisis de Crecimiento Urbano en el Cantón de Liberia (2017-2025)

## 🎯 Objetivo del Proyecto
El objetivo principal de este análisis es cuantificar y visualizar la expansión de la mancha urbana (área construida) en los distintos barrios que conforman el cantón de Liberia, Costa Rica, evaluando los cambios ocurridos entre los años **2017** y **2025**.

Link del visor:  https://asoto59g.github.io/Crecimiento_Urbano_Liberia_2017-2025/
---

## 🛠️ Metodología y Tecnologías Aplicadas
El análisis se ejecutó de forma íntegra en la nube mediante **Google Earth Engine (GEE)**, aprovechando modelos avanzados de Inteligencia Artificial (Machine Learning) para la clasificación de imágenes satelitales.

### 1. Datos de Entrada (Insumos)
* **Límites Territoriales**: Se empleó una capa vectorial (Shapefile/Asset) con los polígonos actualizados de los barrios de Liberia para el 2026 (`projects/ee-oasotob/assets/Barrios_Liberia_2026`).
* **Imágenes Satelitales**: Se utilizó el dataset avanzado **`GOOGLE/SATELLITE_EMBEDDING/V1/ANNUAL`**. 
  * *¿Qué es?* Es un conjunto de datos global desarrollado por Google DeepMind que no contiene colores tradicionales, sino **vectores de 64 dimensiones (Embeddings)**. Estos vectores comprimen datos de múltiples sensores (Sentinel-1, Sentinel-2, Landsat, etc.) capturados durante todo un año para cada píxel de 10 metros, logrando una "huella digital" del comportamiento del terreno en ese periodo.

### 2. Entrenamiento del Modelo de Inteligencia Artificial (Machine Learning)
Dado que los *Embeddings* son dimensiones matemáticas abstractas, se requirió entrenar un modelo que aprendiera a interpretarlas:
1. **Verdad Terreno**: Se utilizó la clasificación de **Dynamic World** (clase 6: "Built/Área Construida") del año **2020** como dato de referencia.
2. **Entrenamiento (Random Forest)**: Se extrajeron aleatoriamente 3,000 muestras del terreno. Se utilizó un algoritmo `smileRandomForest` de GEE para relacionar las 64 dimensiones del embedding de 2020 con las etiquetas de "urbano" y "no urbano". 
3. Este enfoque es altamente resistente a factores como sombras de nubes, cambios estacionales en la vegetación o techos de colores inusuales, ya que el modelo aprende el perfil temporal completo del píxel.

### 3. Clasificación e Inferencia (2017 vs 2025)
Con el modelo de Random Forest entrenado, se procedió a clasificar los *Embeddings* de los años **2017** y **2025** (o 2024 en función del cierre del año). 
* El modelo analizó las 64 dimensiones de cada píxel en esos años y predijo si correspondía a un área urbanizada o no.
* Esto permitió generar máscaras binarias precisas de la infraestructura urbana para el inicio y el final de la serie de tiempo.

### 4. Análisis Espacial y Cuantificación
* **Cálculo de Área**: El área de los píxeles clasificados como urbanos (resolución de 10x10 metros) se transformó a **Hectáreas** dividiendo entre 10,000.
* **Geoprocesamiento (`reduceRegions`)**: Se calculó la sumatoria total de hectáreas urbanizadas que cayeron dentro del polígono de cada barrio individual.
* **Cálculo de Crecimiento**: Para cada barrio se calculó:
  * *Crecimiento Absoluto*: Área Urbana 2025 (ha) - Área Urbana 2017 (ha).
  * *Crecimiento Relativo (%)*: (Crecimiento Absoluto / Área Urbana 2017) * 100.

### 5. Visualización y Resultados
* **Visor Geográfico**: Se generó una visualización apilada donde el fondo muestra el estado inicial (2017 en color naranja), sobre el cual se superpone el estado actual (2025 en color rojo), permitiendo identificar visualmente las franjas de nueva expansión. Por último, se colocaron los polígonos de los barrios (bordes azules transparentes) en la capa superior para brindar contexto espacial.
* **Simbología y Gráficos**: Se automatizó la creación de una leyenda explicativa en el mapa y la generación de un gráfico de columnas interactivo que compara directamente el volumen de hectáreas urbanas del año 2017 contra el 2025 para cada barrio de Liberia.

---

## 🚀 Cómo replicar
Todo este flujo de trabajo se encuentra codificado en un script de JavaScript de *Google Earth Engine Code Editor*. Para compartir los resultados interactivamente con terceros, el código está optimizado para publicarse como una **Earth Engine App** de acceso público con un solo clic, sin requerir configuraciones adicionales de servidores o bases de datos locales.
