## Fase 1: Preparación del entorno

Para descargar el dataset se ha subido el archivo zip a google drive y se ha utilizado la librería `gdown` para descargarlo.
Una vez descargado se descomprime el archivo, este contiene imágenes de 7 clases diferentes agrupadas en carpetas train, test y valid (validation).

---
-> Exploración inicial
- Clases
- Anotaciones
- Tamaños imágenes
---

A continuación se procede a etiquetar el dataset, aunque el conjunto de datos sugiere una sobrepoblación de peces en las imágenes, que clase de animal marino predomina en cada imagen se determinará a partir del área y número de veces que aparece una clase.

Se define el siguiente criterio:
$$
\text{Dominant Class} = \arg\max_{c \in \mathcal{C}} \left( \text{area}_c \cdot \text{count}_c \right)
$$
Para una imagen la clase predominante será aquella cuyo producto área por número de veces que aparece sea mayor.

La información que permite este cálculo se encuentra en los archivos csv de anotación disponibles en cada una de las carpetas train, test y valid. 
Estos archivos contienen las asociaciones entre entidad e imagen, y en particular contienen los bounding boxes de los animales identificados en cada imagen. 

Para obtener el conteo de una clase en cada imagen se agrupan las anotaciones por imagen y clase, y se aplica el método .size() que nos devuelve el tamaño de las agrupaciones.

Para obtener el área de una clase primero se calcula para cada entidad y posteriormente se agrupa por imagen y clase para aplicar el método .sum() que nos devuelve la suma de áreas de una clase para cada imagen.

Como consideraciones a futuro se podría tener en cuenta si existe superposición de bounding boxes y en su caso adaptar el cálculo de área, para esta práctica por no especificarse un criterio y ser un enfoque simple no se tienen en cuenta las intersecciones.

Se tiene entonces pares (imagen, clase), para guardar este cálculo en forma de organización del repositorio, se crean en los sets de train, test y valid subcarpetas con nombres las etiquetas y se mueven las imágenes a sus respectivas carpetas.

El algoritmo que define esta organización pasa por obtener los nuevos paths utilizando los nombres de imágenes y añadiendo las etiquetas correspondientes. El módulo shutil de Python nos permite mover los archivos y con un bloque try-except se evita que se produzcan errores si alguno de los archivos no se encuentra (esperable si ya se han movido a las subcarpetas).

Para el pipeline se utiliza un tipo de dato nativo de tensorflow, el tensorflow.data.Dataset, este tipo de objeto es muy conveniente porque permite definir de manera sencilla y secuencial los pasos para transformar los datos en inputs adecuados para modelos de aprendizaje construidos con tensorflow. Se trata de un objeto por el que pasan y se pueden hacer operaciones con tensores en un formato eficiente.

Como entradas al pipeline se tienen los paths a las imágenes en los cuales están las etiquetas correspondientes (las imágenes han sido organizadas previamente en sus subcarpetas de clase). 

Utilizando tf.data.Dataset.list_files se obtiene el objeto tf.data.Dataset que almacena tensores de paths y son estas cadenas binarias las que se convierten en pares (imágen, etiqueta) tras una serie de pasos:
1. Se obtienen las etiquetas en formato one-hot encoded: extrayendo del path el nombre de la etiqueta y mapeandolo a un entero utilizando una tabla hash se obtiene el entero asociado a la clase, que finalmente se convierte a formato one-hot. En este punto el pipeline devuelve pares path a la imagen y etiqueta en formato one-hot.
2. Se obtienen las imagenes: se lee la imagen con tf.io.read_file y se convierte a tensor con tf.io.decode_image. En este punto el pipeline devuelve pares imagen y etiqueta.
3. Redimensionado de las imágenes: Las imágenes anteriores son de tramaño variable, mientras que los modelos que se van a utilizar esperan datos para entrenar con las mismas dimensiones, primero se añade padding para conseguir que todas las imágenes sean 1024x1024 y después se redimensionan para utilizar un tamaño 64x64 como entrada. 
4. Media 0 y desviación 1: ...


