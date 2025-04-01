## Fase 1: Preparación del entorno

Para descargar el dataset se ha subido el archivo zip a google drive y se ha utilizado la librería `gdown` para descargarlo.
Una vez descargado se descomprime el archivo, este contiene imágenes de 7 clases diferentes agrupadas en carpetas train, test y valid (validation).

-> Exploración inicial

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

El algoritmo que define esta organización pasa por obtener los nuevos paths utilizando los nombres de imágenes y añadiendo las etiquetas correspondientes. El módulo shutil de Python nos permite mover los archivos y con un bloque try-except se evita que se produzcan errores si alguno de los archivos no se encuentra.
