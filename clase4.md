# Detección de anomalías en accesos de usuarios con Apache Spark

Ejemplo práctico de **detección de comportamientos anómalos en un sistema** usando **Apache Spark**.

El objetivo es identificar usuarios cuyo comportamiento **se aleja del patrón normal** utilizando técnicas simples de **estadística y clustering**.

Se simularán **200 registros de actividad** de usuarios y se introducirán algunos casos anómalos.

Posibles anomalías detectadas:

- accesos excesivos
- muchos intentos fallidos
- accesos en horarios poco habituales

---

# 1. Arrancar Apache Spark con Docker

Ejecutar en terminal:

```bash
docker run -it --rm -p 4040:4040 apache/spark:latest bash
```

Entrar en Spark:

```bash
/opt/spark/bin/spark-shell
```

Esto abrirá el entorno interactivo de Apache Spark.

---

# 2. Crear un dataset de actividad de usuarios

Salir temporalmente del shell de Spark o abrir otra terminal dentro del contenedor.

Crear el archivo:

```bash
echo "" > user_activity.csv
```

Generar **200 registros simulados**:

```bash
for i in {1..200}
do
  user="user$((RANDOM%20))"
  accesses=$((RANDOM%6+1))
  failed=$((RANDOM%2))
  hour=$((RANDOM%24))

  echo "$user,$accesses,$failed,$hour" >> user_activity.csv
done
```

Añadir algunos **comportamientos anómalos**:

```bash
echo "hacker1,40,15,3" >> user_activity.csv
echo "hacker2,50,20,2" >> user_activity.csv
echo "hacker3,35,18,4" >> user_activity.csv
```

Ver los primeros registros:

```bash
head user_activity.csv
```

Ejemplo de salida:

```
user1,3,0,10
user5,2,1,14
user2,4,0,9
user8,1,0,13
hacker1,40,15,3
```

Columnas del dataset:

```
usuario,accesos,intentos_fallidos,hora
```

---

# 3. Cargar los datos en Spark

Dentro de `spark-shell`:

```scala
val data = spark.read
  .option("inferSchema","true")
  .csv("user_activity.csv")
  .toDF("user","accesses","failed","hour")

data.show()
```

---

# 4. Analizar el comportamiento general

Spark permite obtener estadísticas del dataset.

```scala
data.describe().show()
```

Esto permite observar:

- media de accesos
- distribución de intentos fallidos
- comportamiento general del sistema

---

# 5. Detectar anomalías con reglas simples

Usuarios con muchos accesos o demasiados intentos fallidos.

```scala
import spark.implicits._

val anomalies = data.filter(
  $"accesses" > 20 || $"failed" > 10
)

anomalies.show()
```

Salida esperada:

```
hacker1 40 15 3
hacker2 50 20 2
hacker3 35 18 4
```

---

# 6. Preparar datos para Machine Learning

Importar librerías de Spark ML:

```scala
import org.apache.spark.ml.feature.VectorAssembler
import org.apache.spark.ml.clustering.KMeans
```

Crear vector de características:

```scala
val assembler = new VectorAssembler()
  .setInputCols(Array("accesses","failed","hour"))
  .setOutputCol("features")

val dataset = assembler.transform(data)
```

---

# 7. Aplicar clustering con K-Means

Agrupar comportamientos similares.

```scala
val kmeans = new KMeans()
  .setK(3)
  .setSeed(1L)

val model = kmeans.fit(dataset)
```

Predecir clusters:

```scala
val predictions = model.transform(dataset)

predictions.select("user","accesses","failed","hour","prediction").show()
```

---

# 8. Analizar clusters generados

```scala
predictions.groupBy("prediction").count().show()
```

Un cluster pequeño puede indicar **comportamientos anómalos**.

---

# 9. Filtrar comportamientos sospechosos

```scala
predictions.filter($"accesses" > 20).show()
```

Resultado esperado:

```
hacker1 40 15 3
hacker2 50 20 2
hacker3 35 18 4
```

---

# 10. Aplicaciones reales

Este tipo de análisis se utiliza en:

- detección de intrusiones
- fraude financiero
- análisis de logs de seguridad
- detección de bots
- comportamiento anómalo de usuarios

---

# 11. Arquitectura típica en Big Data

```
logs → Kafka → Spark Streaming → detección de anomalías → alertas
```

-----------------
-----------------

# Detección de patrones con redes neuronales usando Apache Spark

Ejemplo práctico de **uso de redes neuronales para detectar patrones en datos** usando **Apache Spark MLlib**.

Las redes neuronales son modelos inspirados en el funcionamiento del cerebro humano que permiten aprender relaciones complejas entre datos.

En este laboratorio se simularán **registros de actividad de usuarios** y se entrenará una **red neuronal simple** para clasificar comportamientos normales y anómalos.

---

# 1. Arrancar Apache Spark con Docker

Ejecutar en terminal:

```bash
docker run -it --rm -p 4040:4040 apache/spark:latest bash
```

Entrar en Spark:

```bash
/opt/spark/bin/spark-shell
```

---

# 2. Generar un dataset de ejemplo

Crear archivo de datos:

```bash
echo "" > activity_nn.csv
```

Generar datos simulados:

```bash
for i in {1..200}
do
  accesses=$((RANDOM%6+1))
  failed=$((RANDOM%2))
  hour=$((RANDOM%24))

  label=0

  echo "$accesses,$failed,$hour,$label" >> activity_nn.csv
done
```

Añadir casos anómalos:

```bash
echo "40,15,3,1" >> activity_nn.csv
echo "50,20,2,1" >> activity_nn.csv
echo "35,18,4,1" >> activity_nn.csv
```

Columnas:

```
accesos,intentos_fallidos,hora,label
```

---

# 3. Cargar datos en Spark

Dentro de `spark-shell`:

```scala
val data = spark.read
  .option("inferSchema","true")
  .csv("activity_nn.csv")
  .toDF("accesses","failed","hour","label")

data.show()
```

---

# 4. Preparar los datos

Convertir variables en un vector de características.

```scala
import org.apache.spark.ml.feature.VectorAssembler

val assembler = new VectorAssembler()
  .setInputCols(Array("accesses","failed","hour"))
  .setOutputCol("features")

val dataset = assembler.transform(data)
```

---

# 5. Crear una red neuronal

Spark incluye **Multilayer Perceptron**, una red neuronal simple.

Importar librería:

```scala
import org.apache.spark.ml.classification.MultilayerPerceptronClassifier
```

Definir arquitectura de la red:

```scala
val layers = Array[Int](3, 5, 4, 2)
```

Explicación:

- 3 neuronas de entrada (accesos, fallos, hora)
- 2 capas ocultas
- 2 clases de salida (normal / anómalo)

Crear modelo:

```scala
val trainer = new MultilayerPerceptronClassifier()
  .setLayers(layers)
  .setLabelCol("label")
  .setFeaturesCol("features")
  .setMaxIter(100)
```

---

# 6. Entrenar el modelo

```scala
val model = trainer.fit(dataset)
```

---

# 7. Realizar predicciones

```scala
val result = model.transform(dataset)

result.select("accesses","failed","hour","prediction").show()
```

Ejemplo de salida:

```
accesses failed hour prediction
3       0      10   0
2       1      14   0
40      15     3    1
50      20     2    1
```

---

# 8. Qué hace la red neuronal

La red aprende patrones como:

- accesos normales
- pocos intentos fallidos
- horarios habituales

Cuando detecta valores **muy diferentes**, los clasifica como anomalías.

---

# 9. Aplicaciones reales

Las redes neuronales se utilizan para:

- detección de fraude financiero
- detección de intrusiones
- reconocimiento de imágenes
- análisis de voz
- clasificación de comportamiento de usuarios

---

# 10. Relación con Deep Learning

El **deep learning** es una evolución de las redes neuronales donde se utilizan muchas capas ocultas.

Ejemplos:

- redes convolucionales (CNN)
- redes recurrentes (RNN)
- transformers

Estas arquitecturas permiten analizar:

- imágenes
- vídeo
- audio
- lenguaje natural

En entornos Big Data es habitual combinar:

```
Spark → procesamiento de datos
TensorFlow / PyTorch → deep learning
```

--------
--------

# Reconocer emociones en imágenes de rostros
* https://www.jesusninoc.com/04/12/reconocer-emociones-de-las-caras-de-las-personas-que-hay-en-una-imagen-utilizando-el-modelo-fer-facial-expression-recognition/

-----------------
-----------------

