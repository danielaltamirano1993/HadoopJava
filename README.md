# Hadoop
Configuraciones, programas, ejemplos, ... relacionados con Hadoop

## Cluster Hadoop-Docker
En el archivo docker-compose.yml se encuentra la configuración del cluster, el cual se compone de:

### HDFS
Un NameNode (namenode)

Un DataNode (datanode1)

<!-- Tres DataNode (datanode1, datanode2, datanode3) -->

### YARN
Un ResourceManager (resourcemanager)

Un NodeManager (nodemanager)

Un HistoryServer (historyserver)

(NOTA: este docker-compose.yml es tomado de https://github.com/big-data-europe/docker-hadoop; el archivo hadoop.env es también tomado del mismo sitio)

### Ejecución
Arranque el cluster mediante `docker-compose -d up` (debe estar en el mismo directorio que el archivo docker-compose.yml

Esta parte puede demorar bastante hasta que se descarguen todas las imágenes y el sistema se estabilice

Eventualmente, si ejecuta (en otro terminal) `docker ps`, los seis contenedores deberían indicar en su status `(healthy)`

Vamos a probar ahora con los ejemplos en https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-mapreduce-examples/3.2.1/hadoop-mapreduce-examples-3.2.1.jar así que descargue este archivo

Cargue el archivo en el namenode (preferentemente) o el resourcemanager (en teoría cualquier nodo del cluster debería funcionar) `docker cp hadoop-mapreduce-examples-3.2.1.jar namenode:.`

Para los datos, vamos a usar un pequeño archivo de texto (la Biblia en inglés) en el primer ejemplo http://www.gutenberg.org/cache/epub/10/pg10.txt

Cargue este archivo también en el namenode `docker cp pg10.txt namenode:.`

Ahora abrimos un terminal en el namenode `docker exec -it namenode bash`

Vamos a crear un directorio en hdfs para subir el archivo de texto que queremos procesar `hdfs dfs -mkdir -p input`

Pasemos ahora el archivo al directori input en hdfs `hdfs dfs -put pg10.txt input/`

Ahora vamos a ejecutar el ejemplo wordcount, que contará cuántas veces aparece cada palabra en pg10.txt. El resultado se grabará en `output`, en el archivo `part-r-00000`. Ejecutemos `hadoop jar hadoop-mapreduce-examples-3.2.1.jar wordcount input output` y esperemos que termine

Podemos ver el contenido del archivo con el resultado del conteo de palabras `hdfs dfs -cat output/part-r-00000` y recuperarlo de hdfs copiándolo en el directorio local del contenedor `hdfs dfs -get output/part-r-00000`

Ya podemos salir de namenode `exit` y recuperar el archivo con el resultado para analizarlo `docker cp namenode:part-r-00000 .`

Si desea puede finalizar la ejecución del cluster `docker-compose down`
