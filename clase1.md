# Herramientas
* https://github.com/jesusninoc/ClasesBGD
* Ollama
* https://idx.google.com/u/5/
* https://colab.research.google.com/
* Extensiones https://github.com/jesusninoc/Extension_Chrome_Ollama_Assistant
* https://www.docker.com/
* https://replit.com/
* GitHub Codespaces https://github.com/codespaces

-------------
-------------

# Introducción MySQL

## Configuración de MySQL
```Bash
docker run --platform linux/amd64 --name myXampp -p 41061:22 -p 41062:80 -d -v ~/my_web_pages:/www tomsik68/xampp:8
```

## PhpMyadmin
  - http://localhost:41062/phpmyadmin/
  - https://demo.phpmyadmin.net/master-config/public/

## Fichero SQL y consultas

```SQL
-- =========================================================
-- CREAR BASE DE DATOS
-- =========================================================
-- Esta base de datos agrupa ejemplos sencillos de ciberseguridad
-- para practicar SQL en consultas, filtros y agrupamientos.

CREATE DATABASE IF NOT EXISTS ciberseguridad_sql;
USE ciberseguridad_sql;

-- =========================================================
-- TABLA 1: INTENTOS FALLIDOS DE ACCESO
-- =========================================================
-- Esta tabla sirve para registrar accesos fallidos desde distintas IP.
-- Nos permitirá identificar IPs con muchos intentos fallidos,
-- algo típico en ataques de fuerza bruta.

DROP TABLE IF EXISTS intentos_fallidos;

CREATE TABLE intentos_fallidos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario VARCHAR(50) NOT NULL,
    ip_origen VARCHAR(45) NOT NULL,
    fecha_hora DATETIME NOT NULL,
    intentos_fallidos INT NOT NULL,
    servicio VARCHAR(30) NOT NULL
);

-- Insertamos algunos datos de ejemplo
INSERT INTO intentos_fallidos (usuario, ip_origen, fecha_hora, intentos_fallidos, servicio) VALUES
('admin', '192.168.1.10', '2026-03-12 08:15:00', 2, 'SSH'),
('admin', '192.168.1.10', '2026-03-12 08:20:00', 3, 'SSH'),
('maria', '10.0.0.5', '2026-03-12 09:00:00', 1, 'VPN'),
('admin', '203.0.113.45', '2026-03-12 02:10:00', 5, 'SSH'),
('admin', '203.0.113.45', '2026-03-12 02:15:00', 4, 'SSH'),
('juan', '198.51.100.77', '2026-03-12 11:30:00', 1, 'Web'),
('root', '203.0.113.45', '2026-03-12 02:18:00', 6, 'SSH');

-- =========================================================
-- CONSULTA DE EJEMPLO 1
-- =========================================================
-- Mostrar todos los registros de la tabla
SELECT * FROM intentos_fallidos;

-- =========================================================
-- CONSULTA DE EJEMPLO 2
-- =========================================================
-- Identificar IPs con muchos intentos fallidos acumulados
-- Esta consulta suma los intentos por IP
SELECT 
    ip_origen,
    SUM(intentos_fallidos) AS total_intentos_fallidos
FROM intentos_fallidos
GROUP BY ip_origen
HAVING SUM(intentos_fallidos) >= 5;

-- =========================================================
-- TABLA 2: HORARIOS INUSUALES DE CONEXIÓN
-- =========================================================
-- Esta tabla registra accesos correctos o normales al sistema,
-- pero sirve para detectar conexiones realizadas a horas extrañas,
-- por ejemplo de madrugada.

DROP TABLE IF EXISTS horarios_inusuales;

CREATE TABLE horarios_inusuales (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario VARCHAR(50) NOT NULL,
    ip_origen VARCHAR(45) NOT NULL,
    fecha_hora DATETIME NOT NULL,
    ubicacion VARCHAR(100),
    dispositivo VARCHAR(50)
);

-- Insertamos datos de ejemplo
INSERT INTO horarios_inusuales (usuario, ip_origen, fecha_hora, ubicacion, dispositivo) VALUES
('ana', '192.168.1.20', '2026-03-12 09:05:00', 'Madrid', 'Portátil corporativo'),
('ana', '192.168.1.20', '2026-03-12 09:10:00', 'Madrid', 'Portátil corporativo'),
('luis', '10.0.0.12', '2026-03-12 03:12:00', 'Sevilla', 'Portátil personal'),
('marta', '10.0.0.13', '2026-03-12 14:25:00', 'Valencia', 'PC oficina'),
('luis', '10.0.0.12', '2026-03-13 02:50:00', 'Sevilla', 'Portátil personal'),
('carlos', '203.0.113.100', '2026-03-13 23:48:00', 'Bilbao', 'Móvil'),
('sofia', '198.51.100.10', '2026-03-13 04:15:00', 'Barcelona', 'PC remoto');

-- =========================================================
-- CONSULTA DE EJEMPLO 3
-- =========================================================
-- Mostrar todos los accesos registrados
SELECT * FROM horarios_inusuales;

-- =========================================================
-- CONSULTA DE EJEMPLO 4
-- =========================================================
-- Detectar accesos en horario inusual
-- Aquí consideramos inusual conectarse entre las 00:00 y las 05:59
SELECT 
    usuario,
    ip_origen,
    fecha_hora,
    ubicacion,
    dispositivo
FROM horarios_inusuales
WHERE HOUR(fecha_hora) BETWEEN 0 AND 5;

-- =========================================================
-- TABLA 3: FRAUDE O FUGA DE INFORMACIÓN
-- =========================================================
-- Esta tabla registra eventos sensibles, por ejemplo:
-- - descargas masivas
-- - envío de muchos datos
-- - acceso a archivos confidenciales
-- - copias sospechosas
-- Con esto se pueden analizar patrones anómalos.

DROP TABLE IF EXISTS fraude_fuga_informacion;

CREATE TABLE fraude_fuga_informacion (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario VARCHAR(50) NOT NULL,
    accion VARCHAR(50) NOT NULL,
    recurso VARCHAR(100) NOT NULL,
    volumen_mb DECIMAL(10,2) NOT NULL,
    fecha_hora DATETIME NOT NULL,
    destino VARCHAR(100),
    nivel_riesgo VARCHAR(20)
);

-- Insertamos datos de ejemplo
INSERT INTO fraude_fuga_informacion (usuario, accion, recurso, volumen_mb, fecha_hora, destino, nivel_riesgo) VALUES
('ana', 'descarga', 'informe_financiero.pdf', 2.50, '2026-03-12 10:00:00', 'equipo_local', 'bajo'),
('ana', 'descarga', 'clientes_2026.xlsx', 4.20, '2026-03-12 10:05:00', 'equipo_local', 'medio'),
('luis', 'copia_usb', 'base_datos_clientes.sql', 850.00, '2026-03-12 03:30:00', 'USB externo', 'alto'),
('marta', 'envio', 'contratos.zip', 15.00, '2026-03-12 12:10:00', 'correo_externo', 'medio'),
('luis', 'subida', 'documentacion_confidencial.rar', 920.00, '2026-03-12 03:45:00', 'nube_personal', 'alto'),
('sofia', 'lectura', 'manual_seguridad.pdf', 1.20, '2026-03-12 16:00:00', 'intranet', 'bajo'),
('luis', 'descarga', 'nominas_2026.xlsx', 600.00, '2026-03-12 03:50:00', 'equipo_local', 'alto');

-- =========================================================
-- CONSULTA DE EJEMPLO 5
-- =========================================================
-- Ver todos los eventos sensibles
SELECT * FROM fraude_fuga_informacion;

-- =========================================================
-- CONSULTA DE EJEMPLO 6
-- =========================================================
-- Detectar acciones con gran volumen de datos
-- Aquí buscamos eventos de más de 100 MB
SELECT 
    usuario,
    accion,
    recurso,
    volumen_mb,
    fecha_hora,
    destino,
    nivel_riesgo
FROM fraude_fuga_informacion
WHERE volumen_mb > 100;

-- =========================================================
-- CONSULTA DE EJEMPLO 7
-- =========================================================
-- Analizar qué usuario ha movido más datos en total
SELECT
    usuario,
    SUM(volumen_mb) AS total_mb_movidos
FROM fraude_fuga_informacion
GROUP BY usuario
ORDER BY total_mb_movidos DESC;

-- =========================================================
-- CONSULTA DE EJEMPLO 8
-- =========================================================
-- Detectar usuarios con varios eventos de riesgo alto
SELECT
    usuario,
    COUNT(*) AS total_eventos_altos
FROM fraude_fuga_informacion
WHERE nivel_riesgo = 'alto'
GROUP BY usuario
HAVING COUNT(*) >= 2;
```

## Consultas

```SQL
-- =========================================================
-- CONSULTAS CLAVE PARA CLASE
-- Ejemplos sencillos de SQL aplicados a ciberseguridad
-- =========================================================

-- =========================================================
-- A) IDENTIFICAR IPs CON MUCHOS INTENTOS FALLIDOS
-- =========================================================
-- Esta consulta agrupa los intentos fallidos por dirección IP
-- y muestra solo aquellas IPs que han acumulado 5 o más intentos.
-- Esto puede indicar un posible ataque de fuerza bruta.

SELECT 
    ip_origen,
    SUM(intentos_fallidos) AS total_intentos_fallidos
FROM intentos_fallidos
GROUP BY ip_origen
HAVING SUM(intentos_fallidos) >= 5;



-- =========================================================
-- B) DETECTAR CONEXIONES EN HORARIOS INUSUALES
-- =========================================================
-- Se muestran accesos que ocurren entre las 00:00 y las 05:59.
-- Muchas organizaciones consideran estas horas sospechosas
-- si el usuario normalmente trabaja en horario laboral.

SELECT 
    usuario,
    ip_origen,
    fecha_hora
FROM horarios_inusuales
WHERE HOUR(fecha_hora) BETWEEN 0 AND 5;



-- =========================================================
-- C) USUARIO QUE MÁS DATOS HA MOVIDO
-- =========================================================
-- Se calcula el volumen total de datos transferidos por usuario.
-- Esto permite detectar posibles fugas de información
-- o descargas masivas de archivos sensibles.

SELECT
    usuario,
    SUM(volumen_mb) AS total_mb_movidos
FROM fraude_fuga_informacion
GROUP BY usuario
ORDER BY total_mb_movidos DESC;
```

---------------
---------------

# Auditoría

## Control real

### MySQL oficial
    
```Bash
docker run --name mysql-demo \
-e MYSQL_ROOT_PASSWORD=root \
-p 3306:3306 \
-d mysql:8
```

### Script SQL

```SQL
-- =====================================================
-- DEMO SEGURIDAD BASE DE DATOS
-- =====================================================

DROP DATABASE IF EXISTS demo_seguridad;
CREATE DATABASE demo_seguridad;
USE demo_seguridad;

-- =====================================================
-- TABLA EMPLEADOS
-- =====================================================

CREATE TABLE empleados (
id INT AUTO_INCREMENT PRIMARY KEY,
nombre VARCHAR(50),
puesto VARCHAR(50),
salario INT
);

INSERT INTO empleados(nombre,puesto,salario) VALUES
('Ana','Analista',32000),
('Carlos','Administrador Sistemas',38000),
('Marta','Marketing',42000),
('Luis','Director General',150000);

-- =====================================================
-- TABLA LOG DE INTENTOS
-- =====================================================

CREATE TABLE log_accesos_salarios (
id INT AUTO_INCREMENT PRIMARY KEY,
usuario_mysql VARCHAR(100),
fecha DATETIME,
evento VARCHAR(200)
);

-- =====================================================
-- PROCEDIMIENTO CONTROLADO
-- =====================================================

DELIMITER $$

CREATE PROCEDURE ver_salario(IN nombre_empleado VARCHAR(50))
BEGIN

IF nombre_empleado = 'Luis' THEN

INSERT INTO log_accesos_salarios(usuario_mysql,fecha,evento)
VALUES(
CURRENT_USER(),
NOW(),
'Intento de consulta del salario del Director General'
);

SIGNAL SQLSTATE '45000'
SET MESSAGE_TEXT='ACCESO DENEGADO: salario protegido';

ELSE

SELECT nombre,puesto,salario
FROM empleados
WHERE nombre = nombre_empleado;

END IF;

END$$

DELIMITER ;
```

### Consultas de comprobación

```Bash
CALL ver_salario('Ana');
CALL ver_salario('Luis');
SELECT * FROM log_accesos_salarios;
```

---------------
---------------

# Hash, copia de seguridad y cifrado

## Hash y copia de seguridad

```SQL
-- =====================================================
-- DEMO SEGURIDAD Y AUDITORÍA EN BASE DE DATOS
-- =====================================================

DROP DATABASE IF EXISTS empresa_demo;
CREATE DATABASE empresa_demo;
USE empresa_demo;

-- =====================================================
-- TABLA EMPLEADOS (DATOS REALES)
-- =====================================================

CREATE TABLE empleados (
id INT AUTO_INCREMENT PRIMARY KEY,
nombre VARCHAR(50),
puesto VARCHAR(50),
salario INT
);

INSERT INTO empleados(nombre,puesto,salario) VALUES
('Ana','Analista',32000),
('Carlos','Administrador Sistemas',38000),
('Marta','Marketing',42000);

-- =====================================================
-- TABLA BACKUP DE EMPLEADOS
-- =====================================================
-- Aquí se guardará automáticamente una copia
-- cada vez que se inserte un nuevo empleado

CREATE TABLE empleados_backup (
id INT,
nombre VARCHAR(50),
puesto VARCHAR(50),
salario INT,
fecha_backup DATETIME
);

-- =====================================================
-- TRIGGER DE BACKUP AUTOMÁTICO
-- =====================================================

DELIMITER $$

CREATE TRIGGER backup_empleados
AFTER INSERT ON empleados
FOR EACH ROW
BEGIN

INSERT INTO empleados_backup
VALUES(
NEW.id,
NEW.nombre,
NEW.puesto,
NEW.salario,
NOW()
);

END$$

DELIMITER ;

-- =====================================================
-- VISTA CON SUELDO HASHEADO
-- =====================================================
-- Oculta el sueldo real usando SHA2

CREATE VIEW empleados_seguridad AS
SELECT
nombre,
puesto,
SHA2(salario,256) AS salario_hash
FROM empleados;
```

### Consultas para ver tabla con hash y dato copiado
```MySQL
SELECT * FROM empleados_seguridad;

INSERT INTO empleados(nombre,puesto,salario)
VALUES('Luis','Director',90000);

SELECT * FROM empleados_backup;
```


## Cifrar y descifrar datos en tablas con vistas

```SQL
-- =====================================================
-- DEMO CIFRADO AES EN MYSQL
-- =====================================================

DROP DATABASE IF EXISTS empresa_cifrado;
CREATE DATABASE empresa_cifrado;
USE empresa_cifrado;

-- =====================================================
-- TABLA EMPLEADOS
-- =====================================================

CREATE TABLE empleados (
id INT AUTO_INCREMENT PRIMARY KEY,
nombre VARCHAR(50),
puesto VARCHAR(50),
salario INT
);

INSERT INTO empleados(nombre,puesto,salario) VALUES
('Ana','Analista',32000),
('Carlos','Administrador Sistemas',38000),
('Marta','Marketing',42000),
('Luis','Director General',150000);

-- =====================================================
-- TABLA BACKUP AUTOMÁTICO
-- =====================================================

CREATE TABLE empleados_backup (
id INT,
nombre VARCHAR(50),
puesto VARCHAR(50),
salario INT,
fecha_backup DATETIME
);

-- =====================================================
-- TRIGGER DE COPIA AUTOMÁTICA
-- =====================================================

DELIMITER $$

CREATE TRIGGER backup_empleados
AFTER INSERT ON empleados
FOR EACH ROW
BEGIN

INSERT INTO empleados_backup
VALUES(
NEW.id,
NEW.nombre,
NEW.puesto,
NEW.salario,
NOW()
);

END$$

DELIMITER ;

-- =====================================================
-- VISTA CON CIFRADO AES DEL SALARIO
-- =====================================================

CREATE VIEW empleados_cifrados AS
SELECT
nombre,
puesto,
TO_BASE64(AES_ENCRYPT(salario,'clave_super_secreta')) AS salario_cifrado
FROM empleados;

-- =====================================================
-- VISTA QUE DESCIFRA (solo para administradores)
-- =====================================================

CREATE VIEW empleados_descifrado AS
SELECT
nombre,
puesto,
AES_DECRYPT(FROM_BASE64(
TO_BASE64(AES_ENCRYPT(salario,'clave_super_secreta'))
),'clave_super_secreta') AS salario
FROM empleados;
```

### Consultas para ver cifrado

```MySQL
SELECT * FROM empleados_cifrados;

SELECT * FROM empleados_descifrado;
```

---------------
---------------

# Inyección SQL

## Parte 1 (normal)

```SQL
-- =====================================================
-- DEMO INYECCIÓN SQL PARA VER SALARIOS
-- =====================================================

DROP DATABASE IF EXISTS demo_inyeccion_salarios;
CREATE DATABASE demo_inyeccion_salarios;
USE demo_inyeccion_salarios;

-- =====================================================
-- TABLA EMPLEADOS
-- =====================================================

CREATE TABLE empleados (
id INT AUTO_INCREMENT PRIMARY KEY,
usuario VARCHAR(50),
password VARCHAR(50),
puesto VARCHAR(50),
salario INT
);

INSERT INTO empleados(usuario,password,puesto,salario) VALUES
('ana','1234','Analista',32000),
('carlos','abcd','Administrador Sistemas',38000),
('marta','pass','Marketing',42000),
('director','supersecret','Director General',150000);
```

### Consultas
```SQL
SELECT usuario, puesto, salario
FROM empleados
WHERE usuario='ana'
AND password='1234';

SELECT usuario, puesto, salario
FROM empleados
WHERE usuario='ana'
AND password='xxxx';

SELECT usuario, puesto, salario
FROM empleados
WHERE usuario='' OR '1'='1';
```

## Parte 2 (union)

## Parte 1 (normal)

```SQL
-- =====================================================
-- DEMO INYECCIÓN SQL CON UNION
-- =====================================================

DROP DATABASE IF EXISTS demo_union_injection;
CREATE DATABASE demo_union_injection;
USE demo_union_injection;

-- =====================================================
-- TABLA PUBLICA (lo que la aplicación muestra)
-- =====================================================

CREATE TABLE empleados_publicos (
id INT AUTO_INCREMENT PRIMARY KEY,
nombre VARCHAR(50),
departamento VARCHAR(50),
email VARCHAR(100)
);

INSERT INTO empleados_publicos(nombre,departamento,email) VALUES
('Ana','IT','ana@empresa.com'),
('Carlos','Marketing','carlos@empresa.com'),
('Marta','Ventas','marta@empresa.com');

-- =====================================================
-- TABLA SECRETA (no debería verse)
-- =====================================================

CREATE TABLE salarios_secretos (
id INT AUTO_INCREMENT PRIMARY KEY,
nombre VARCHAR(50),
puesto VARCHAR(50),
salario INT
);

INSERT INTO salarios_secretos(nombre,puesto,salario) VALUES
('Ana','Analista',32000),
('Carlos','Administrador Sistemas',38000),
('Marta','Responsable Marketing',42000),
('Luis','Director General',150000);
```

### Consultas
```SQL
SELECT nombre, departamento, email
FROM empleados_publicos
WHERE nombre='Ana';

SELECT nombre, departamento, email
FROM empleados_publicos
WHERE nombre=''
UNION
SELECT nombre,puesto,salario FROM salarios_secretos;
```

---------------
---------------

# Mongo

## 1. Arrancar MongoDB con Docker

Ejecutar en terminal:

```bash
docker run -d \
--name mongo-demo \
-p 27017:27017 \
mongo:7
```

## 2. Entrar en la consola de MongoDB

```bash
docker exec -it mongo-demo mongosh
```

Verás algo así:

```
test>
```

## 3. Crear una base de datos

```javascript
use seguridad_demo
```

## 4. Insertar un documento JSON

```javascript
db.eventos.insertOne({
  usuario: "ana",
  evento: "login",
  ip: "192.168.1.20",
  fecha: "2026-03-12",
  dispositivo: {
    tipo: "portatil",
    sistema: "Windows"
  }
})
```

Resultado:

```
acknowledged: true
```

## 5. Consultar los documentos

```javascript
db.eventos.find()
```

Resultado:

```json
{
  _id: ObjectId("..."),
  usuario: "ana",
  evento: "login",
  ip: "192.168.1.20",
  fecha: "2026-03-12",
  dispositivo: {
    tipo: "portatil",
    sistema: "Windows"
  }
}
```

## 6. Insertar un evento de seguridad

```javascript
db.eventos.insertOne({
  usuario: "admin",
  evento: "login_fallido",
  ip: "203.0.113.45",
  intentos: 5
})
```

## 7. Buscar eventos sospechosos

```javascript
db.eventos.find({ evento: "login_fallido" })
```

## 8. Mostrar los resultados formateados

```javascript
db.eventos.find().pretty()
```

### Ejemplo de documento JSON en NoSQL

```json
{
  "usuario": "admin",
  "evento": "login_fallido",
  "ip": "203.0.113.45",
  "intentos": 5
}
```

---

## Comparación SQL vs NoSQL

### SQL

```
TABLA EVENTOS
usuario | evento | ip | fecha
```

### NoSQL (MongoDB)

```json
{
  "usuario": "ana",
  "evento": "login",
  "ip": "192.168.1.20"
}
```

--------

## Detener el contenedor

```bash
docker stop mongo-demo
```

## Eliminar el contenedor

```bash
docker rm mongo-demo
```

-------------
-------------

# Demo Hadoop con Docker (MapReduce Examples)

Laboratorio sencillo para ejecutar **Hadoop MapReduce usando Docker** y los ejemplos incluidos en Hadoop.

Este laboratorio permite:

- ejecutar Hadoop sin instalar nada
- entender el modelo **Map → Shuffle → Reduce**
- probar jobs MapReduce reales

---

# 1. Arrancar Hadoop con Docker

Ejecutar en terminal:

```bash
docker run -it --rm apache/hadoop:3.4.3 /bin/bash
```

Esto abre un contenedor con Hadoop instalado.

---

# 2. Verificar que Hadoop funciona

```bash
hadoop version
```

Salida esperada:

```
Hadoop 3.4.3
```

---

# 3. Localizar el JAR de ejemplos

```bash
find / -name "hadoop-mapreduce-examples*.jar"
```

Normalmente aparecerá algo así:

```
/opt/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.4.3.jar
```

Guardar la ruta en una variable:

```bash
JAR=$(find /opt -name "hadoop-mapreduce-examples*.jar" | grep -v sources)
```

Comprobar:

```bash
echo $JAR
```

---

# 4. Crear datos de ejemplo

```bash
echo -e "hola mundo hadoop
hola docker
hadoop es big data
docker y hadoop" > texto.txt
```

Ver archivo:

```bash
cat texto.txt
```

---

# 5. Ejecutar WordCount (MapReduce)

```bash
hadoop jar $JAR wordcount texto.txt salida
```

Ver resultado:

```bash
cat salida/part-r-00000
```

Resultado esperado:

```
big 1
data 1
docker 2
es 1
hadoop 3
hola 2
mundo 1
y 1
```

---

# 6. Ejemplo — GREP (buscar patrones)

Crear archivo:

```bash
echo -e "login error
login ok
error acceso
ok acceso
error login" > logs.txt
```

Ejecutar:

```bash
hadoop jar $JAR grep logs.txt salida_grep "error"
```

Ver resultado:

```bash
cat salida_grep/part-r-00000
```

---

# 7. Ejemplo — Calcular π con MapReduce

```bash
hadoop jar $JAR pi 2 1000
```

Resultado típico:

```
Estimated value of Pi is 3.1416
```

---

# 8. Ejemplo — Media de longitud de palabras

```bash
hadoop jar $JAR wordmean texto.txt salida_mean
```

Resultado:

```bash
cat salida_mean/part-r-00000
```

---

# 9. Ejemplo — Mediana de longitud de palabras

```bash
hadoop jar $JAR wordmedian texto.txt salida_median
```

Resultado:

```bash
cat salida_median/part-r-00000
```

---

# 10. Ejemplo — Desviación estándar de palabras

```bash
hadoop jar $JAR wordstandarddeviation texto.txt salida_std
```

Resultado:

```bash
cat salida_std/part-r-00000
```

---

# 11. Ejemplo — WordCount con múltiples archivos

Crear segundo archivo:

```bash
echo -e "big data hadoop
docker hadoop cluster" > texto2.txt
```

Ejecutar:

```bash
hadoop jar $JAR multifilewc texto.txt texto2.txt salida_multi
```

Resultado:

```bash
cat salida_multi/part-r-00000
```

---

# 12. Ejemplo — Generar datos aleatorios

```bash
hadoop jar $JAR randomwriter randomdata
```

Esto genera datos aleatorios para pruebas.

---

# 13. Ejemplo — Generar texto aleatorio

```bash
hadoop jar $JAR randomtextwriter randomtext
```

---

# 14. Ejemplo — Generar dataset para TeraSort

```bash
hadoop jar $JAR teragen 10000 teradata
```

---

# 15. Ejecutar TeraSort

```bash
hadoop jar $JAR terasort teradata terasort_output
```

---

# 16. Validar resultado TeraSort

```bash
hadoop jar $JAR teravalidate terasort_output teravalidate_output
```

---

# 17. Ejemplo — Analizar IPs en logs

Crear archivo:

```bash
echo -e "192.168.1.10 login
192.168.1.11 login
192.168.1.10 login
192.168.1.12 login
192.168.1.10 login
192.168.1.11 login" > web_logs.txt
```

Ejecutar:

```bash
hadoop jar $JAR wordcount web_logs.txt salida_ips
```

Resultado:

```bash
cat salida_ips/part-r-00000
```

Esto permite detectar **IPs con más actividad**.

---

# 18. Ver todos los ejemplos disponibles

```bash
hadoop jar $JAR
```

---

# 19. Limpiar resultados

```bash
rm -r salida*
```

---

# Arquitectura MapReduce

```
Datos → MAP → Shuffle → REDUCE → Resultado
```

Explicación:

| Fase | Descripción |
|-----|-------------|
| MAP | genera pares clave-valor |
| Shuffle | agrupa claves iguales |
| REDUCE | combina resultados |

---

# Detener Hadoop

Salir del contenedor:

```bash
exit
```

Al usar `--rm`, Docker eliminará el contenedor automáticamente.
