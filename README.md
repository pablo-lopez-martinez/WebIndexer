# mri-webindexer

## Descripción

`mri-webindexer` es una aplicación multihilo desarrollada en Java utilizando Maven. Su propósito es procesar archivos de texto `.url` que contienen listas de URLs, descargarlas, analizarlas e indexarlas utilizando Apache Lucene. El proyecto se centra en la eficiencia y calidad del procesamiento, garantizando la correcta indexación de los contenidos descargados, así como la posibilidad de realizar consultas avanzadas sobre el índice generado.

## Funcionalidades

- **Descarga de Páginas Web**: Descarga de páginas web listadas en archivos `.url` utilizando `java.net.http.HttpClient`.
- **Procesamiento Multihilo**: Cada archivo `.url` es procesado por un hilo diferente.
- **Filtrado de Respuestas HTTP**: Solo se procesan las URLs que devuelven un código de respuesta `200 OK`. Opcionalmente, se manejan redirecciones HTTP (`3xx`).
- **Almacenamiento Local**: Las páginas descargadas se almacenan localmente con un nombre derivado de la URL y con extensión `.loc`.
- **Procesamiento HTML**: Utilizando `Jsoup`, se extraen y almacenan el título y el cuerpo de las páginas en archivos `.loc.notags` sin etiquetas HTML.
- **Indexación con Lucene**: Se indexan los archivos `.loc.notags` en Lucene con campos adicionales como `hostname`, `thread`, `locKb`, `notagsKb`, y tiempos de creación, acceso y modificación.
- **Consultas Avanzadas**: Mediante clases adicionales, se puede consultar los términos más relevantes por documento o campo.

## Configuración

### Argumentos de la Línea de Comandos

La clase principal `WebIndexer` acepta los siguientes argumentos:

- `-index INDEX_PATH`: Indica la carpeta donde se almacenará el índice.
- `-docs DOCS_PATH`: Indica la carpeta donde se almacenan los archivos `.loc` y `.loc.notags`.
- `-create`: Crea un nuevo índice (por defecto, se usa `CREATE_OR_APPEND`).
- `-numThreads int`: Define el número de hilos. Si no se indica, se utilizan tantos hilos como núcleos disponibles en el sistema.
- `-h`: Los hilos informan sobre el inicio y fin de su trabajo.
- `-p`: La aplicación informa sobre el final del proceso y el tiempo total de ejecución.
- `-titleTermVectors`: Habilita el almacenamiento de vectores de términos para el campo `title`.
- `-bodyTermVectors`: Habilita el almacenamiento de vectores de términos para el campo `body`.
- `-analyzer Analyzer`: Especifica el `Analyzer` a usar. Por defecto, se usa el `Standard Analyzer`.

## Clases Adicionales

### TopTermsInDoc

La clase `TopTermsInDoc` permite obtener los términos más relevantes en un documento específico dentro del índice. Es útil para analizar la relevancia de los términos dentro de un documento individual, basado en su frecuencia y la frecuencia inversa del documento (TF-IDF).

#### Argumentos:

- `-index path`: Ruta del índice.
- `-field campo`: Campo del índice a analizar.
- `-docID int`: ID del documento a analizar.
- `-url url`: Alternativa a `-docID`, identifica el documento mediante su URL.
- `-top n`: Número de términos a mostrar.
- `-outfile path`: Archivo de salida para los resultados.

### TopTermsInField

La clase `TopTermsInField` permite obtener y listar los términos más frecuentes en un campo específico de un índice Lucene. Es una herramienta útil para análisis de contenido y extracción de palabras clave dentro de un campo particular del índice.

### Argumentos

- `-index path`: Ruta del índice que se quiere analizar.
- `-field campo`: El campo dentro del índice del que se desea extraer los términos más comunes.
- `-top n`: El número de términos más frecuentes que se desea obtener.
- `-outfile path`: Archivo de salida donde se guardarán los resultados. Si no se especifica, los resultados se mostrarán en la consola.


### Archivo de Configuración

Opcionalmente, se puede utilizar un archivo `config.properties` en `src/main/resources` con las siguientes propiedades:

- `onlyDoms`: Procesa solo URLs con los dominios indicados (ej. `.uk`, `.es`, `.com`).

### Ejemplo de Archivo `config.properties`

```properties
onlyDoms=.uk .es .com
