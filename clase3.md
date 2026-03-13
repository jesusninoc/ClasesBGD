# Tipos de minería de datos

- Minería descriptiva
- Minería predictiva
- Minería prescriptiva

-----------
-----------

# Detección de combinaciones de acciones sospechosas en sistemas con Apache Spark

Ejemplo práctico de **análisis de comportamiento de usuarios usando Apache Spark** sin scripts externos.  
Se generarán **200 eventos simulados de actividad**, algunos normales y otros potencialmente sospechosos.

Se detectarán situaciones como:

- accesos fuera de horario habitual
- descargas masivas de archivos
- modificaciones sospechosas
- actividad anómala del usuario admin
- combinaciones de acciones sospechosas
- usuarios con actividad excesiva

---

# 1. Arrancar Spark con Docker

```bash
docker run -it --rm -p 4040:4040 apache/spark:latest bash
```

Entrar en el shell interactivo de Spark:

```bash
/opt/spark/bin/spark-shell
```

---

# 2. Generar un archivo con 200 eventos simulados

Salir temporalmente del shell de Spark o abrir otra terminal dentro del contenedor.

Crear el archivo con el siguiente script:

```bash
echo '#!/bin/bash
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

done' > generar_actividad.sh
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

---

# 3. Cargar actividad en Spark

Volver a `spark-shell`.

```scala
val actividad = spark.read
.option("header","false")
.option("inferSchema","true")
.csv("actividad_usuarios.csv")
.toDF("usuario","accion","archivo","hora")

actividad.show(20,false)
```

---

# 4. Detectar actividad fuera de horario

Se consideran sospechosas las acciones realizadas entre **0 y 5 horas**.

```scala
actividad.filter($"hora" < 6).show(false)
```

Esto puede indicar:

- accesos no habituales
- uso indebido de cuentas
- actividad automatizada

---

# 5. Detectar descargas de archivos

```scala
actividad.filter($"accion" === "descarga").show(false)
```

Muchas descargas pueden indicar:

- copia masiva de datos
- posible extracción de información

---

# 6. Detectar modificaciones de archivos

```scala
actividad.filter($"accion" === "modificacion").show(false)
```

Las modificaciones pueden indicar:

- alteración de información
- manipulación de sistemas

---

# 7. Detectar actividad del usuario admin

```scala
actividad.filter($"usuario" === "admin").show(false)
```

Las cuentas privilegiadas requieren especial monitorización.

---

# 8. Detectar combinaciones sospechosas

Ejemplo: **admin modificando archivos de madrugada**

```scala
actividad.filter(
$"usuario" === "admin" &&
$"accion" === "modificacion" &&
$"hora" < 6
).show(false)
```

Este patrón puede indicar:

- compromiso de cuentas privilegiadas
- abuso de permisos

---

# 9. Detectar usuarios con mayor actividad

```scala
actividad.groupBy("usuario")
.count()
.orderBy(desc("count"))
.show()
```

Usuarios con demasiadas acciones pueden indicar:

- automatización
- comportamiento anómalo

---

# 10. Detectar descargas masivas por usuario

```scala
actividad.filter($"accion" === "descarga")
.groupBy("usuario")
.count()
.orderBy(desc("count"))
.show()
```

Permite identificar usuarios que descargan muchos archivos.

---

# 11. Usar Spark SQL para detección

Registrar tabla temporal:

```scala
actividad.createOrReplaceTempView("actividad")
```

Detectar accesos nocturnos:

```scala
spark.sql("""
SELECT *
FROM actividad
WHERE hora < 6
""").show(false)
```

Detectar actividad sospechosa del admin:

```scala
spark.sql("""
SELECT *
FROM actividad
WHERE usuario = 'admin'
AND accion = 'modificacion'
AND hora < 6
""").show(false)
```

---

# 12. Identificar usuarios con comportamiento sospechoso

```scala
spark.sql("""
SELECT usuario, COUNT(*) as eventos
FROM actividad
WHERE accion='descarga'
OR hora < 6
GROUP BY usuario
ORDER BY eventos DESC
""").show()
```

Esto permite detectar posibles:

- amenazas internas
- abuso de privilegios
- extracción de información
