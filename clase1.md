
## Configuración de MySQL
```Bash
docker run --platform linux/amd64 --name myXampp -p 41061:22 -p 41062:80 -d -v ~/my_web_pages:/www tomsik68/xampp:8
```

## PhpMyadmin:
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

## Más consultas

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
