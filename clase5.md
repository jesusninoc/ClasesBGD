# Introducción a Big Data y análisis de datos  

## Actividad práctica: análisis de comportamiento en sistemas informáticos

---

# 1. Contexto de la actividad

Una empresa tecnológica está revisando los registros de uso de su plataforma interna para detectar comportamientos que puedan indicar **problemas de seguridad o uso anómalo del sistema**.

Durante una semana se han registrado los accesos diarios de varios usuarios al sistema. Además, el equipo de seguridad ha recopilado estadísticas sobre distintos tipos de incidentes detectados en la red corporativa.

El objetivo de esta actividad es analizar los datos disponibles utilizando **herramientas básicas de estadística y análisis de datos**, con el fin de identificar patrones de comportamiento y posibles riesgos de seguridad.

---

# 2. Datos disponibles

## 2.1 Registro semanal de accesos al sistema

| Usuario | Lunes | Martes | Miércoles | Jueves | Viernes |
|--------|------|------|-----------|--------|--------|
| U31 | 5 | 6 | 5 | 4 | 6 |
| U32 | 0 | 0 | 1 | 0 | 0 |
| U33 | 14 | 17 | 15 | 18 | 16 |
| U34 | 2 | 3 | 2 | 2 | 3 |
| U35 | 8 | 7 | 9 | 8 | 10 |

---

## 2.2 Incidentes de seguridad detectados durante la semana

| Tipo de incidente | Número de casos |
|------------------|----------------|
| Malware | 3 |
| Accesos desde IP sospechosa | 6 |
| Correos de phishing | 4 |
| Intentos de fuerza bruta | 2 |
| Error de configuración de seguridad | 1 |

---

# 3. Actividades a realizar

## 3.1 Análisis estadístico básico

A partir de la tabla de accesos:

1. Calcula el **promedio de accesos diarios** para cada usuario.
2. Calcula la **mediana** de accesos de cada usuario.
3. Identifica si existe algún valor que se repita con mayor frecuencia (moda).

Después responde:

- ¿Qué usuario presenta la actividad más alta?
- ¿Qué usuario utiliza el sistema con menor frecuencia?

---

## 3.2 Variabilidad en los accesos

Para cada usuario calcula el **rango de accesos** durante la semana.

El rango se calcula como:

```
valor máximo − valor mínimo
```

A partir de este cálculo indica:

- qué usuario presenta mayor variación de accesos
- qué usuario muestra un comportamiento más estable

---

## 3.3 Interpretación de incidentes de seguridad

Analiza la tabla de incidentes.

1. Calcula el **número total de incidentes registrados**.
2. Calcula el **porcentaje aproximado** de cada tipo de incidente.

Después responde:

- ¿Qué incidente ocurre con mayor frecuencia?
- ¿Cuál es el menos frecuente?
- ¿Cuál de estos incidentes consideras más crítico para una organización?

---

## 3.4 Clasificación de usuarios según actividad

Utilizando la media de accesos clasifica los usuarios en tres categorías:

- **Actividad baja**: menos de 3 accesos de media
- **Actividad media**: entre 3 y 9 accesos
- **Actividad alta**: más de 9 accesos

Indica a qué categoría pertenece cada usuario.

---

## 3.5 Análisis de seguridad

Observando los datos de accesos:

- ¿Hay algún usuario cuyo comportamiento pueda considerarse inusual?
- ¿Qué factores podrían indicar una posible actividad automatizada o sospechosa?

Justifica brevemente tu respuesta.

---

# 4. Resolución

## 4.1 Promedio de accesos

| Usuario | Media |
|------|------|
U31 | 5,2 |
U32 | 0,2 |
U33 | 16 |
U34 | 2,4 |
U35 | 8,4 |

Usuario con mayor actividad: **U33**

Usuario con menor actividad: **U32**

---

## 4.2 Mediana

| Usuario | Mediana |
|------|------|
U31 | 5 |
U32 | 0 |
U33 | 16 |
U34 | 2 |
U35 | 8 |

---

## 4.3 Moda

- U31 → 5 y 6  
- U32 → 0  
- U34 → 2  
- U35 → 8  

U33 no tiene moda.

---

## 4.4 Rango

| Usuario | Rango |
|------|------|
U31 | 2 |
U32 | 1 |
U33 | 4 |
U34 | 1 |
U35 | 3 |

Mayor variación → **U33**

Usuario más estable → **U32 y U34**

---

## 4.5 Total de incidentes

3 + 6 + 4 + 2 + 1 = **16 incidentes**

---

## 4.6 Porcentaje aproximado de incidentes

| Incidente | Porcentaje |
|------|------|
Malware | 19% |
Accesos desde IP sospechosa | 38% |
Phishing | 25% |
Fuerza bruta | 12% |
Error de configuración | 6% |

---

Incidente más frecuente → **Accesos desde IP sospechosa**

Incidente menos frecuente → **Error de configuración**

---

## 4.7 Clasificación de usuarios

Actividad baja:

U32  
U34

Actividad media:

U31  
U35

Actividad alta:

U33

---

## 4.8 Interpretación de seguridad

El usuario **U33** presenta una actividad significativamente mayor que el resto.

Esto podría indicar:

- uso intensivo del sistema
- automatización de tareas
- posible actividad sospechosa

Por este motivo sería recomendable revisar su actividad con mayor detalle.

-------------
-------------

# Introducción a Big Data y gestión de datos  
## Caso práctico: análisis estadístico de un incidente de seguridad

---

# 1. Contexto del caso

Una empresa tecnológica detecta un posible **incidente de seguridad relacionado con intentos de acceso fallidos** en su portal interno de administración.

Durante una semana se han registrado los intentos de acceso fallidos por dirección IP. El equipo de seguridad sospecha que podría tratarse de un **ataque de fuerza bruta**.

El objetivo del análisis es aplicar **estadística básica y funciones de análisis de datos** para identificar patrones, anomalías y posibles riesgos.

---

# 2. Datos del incidente

## 2.1 Intentos de acceso fallidos por IP

| IP | Lunes | Martes | Miércoles | Jueves | Viernes |
|----|------|------|-----------|--------|--------|
| 192.168.1.10 | 2 | 3 | 2 | 3 | 2 |
| 192.168.1.21 | 1 | 1 | 0 | 1 | 0 |
| 192.168.1.35 | 25 | 28 | 30 | 27 | 29 |
| 192.168.1.48 | 3 | 2 | 3 | 2 | 3 |
| 192.168.1.72 | 6 | 7 | 8 | 7 | 6 |

---

# 3. Introducción estadística

## 3.1 Población, muestra y variables

**Población**

Todos los accesos registrados en el sistema durante la semana.

**Muestra**

Los intentos de acceso fallidos de cinco direcciones IP analizadas.

**Variables**

Variables cuantitativas:

- número de intentos fallidos
- frecuencia de accesos

Variable categórica:

- dirección IP

---

# 4. Tipos de datos y escala de medición

Los datos analizados son:

**Cuantitativos**

Número de intentos de acceso.

Escala de medición:

**Escala de razón**

Porque:

- existe un valor cero
- las diferencias son significativas

---

# 5. Medidas de tendencia central

## 5.1 Media

| IP | Media |
|----|------|
192.168.1.10 | 2.4 |
192.168.1.21 | 0.6 |
192.168.1.35 | 27.8 |
192.168.1.48 | 2.6 |
192.168.1.72 | 6.8 |

Interpretación:

La IP **192.168.1.35** presenta una media extremadamente superior al resto.

---

## 5.2 Mediana

| IP | Mediana |
|----|------|
192.168.1.10 | 2 |
192.168.1.21 | 1 |
192.168.1.35 | 28 |
192.168.1.48 | 3 |
192.168.1.72 | 7 |

La mediana confirma que **la IP 192.168.1.35 mantiene valores altos de forma constante**.

---

## 5.3 Moda

| IP | Moda |
|----|------|
192.168.1.10 | 2 |
192.168.1.21 | 1 y 0 |
192.168.1.35 | ninguna |
192.168.1.48 | 2 y 3 |
192.168.1.72 | 6 y 7 |

---

# 6. Medidas de dispersión

## 6.1 Rango

| IP | Máximo | Mínimo | Rango |
|----|------|------|------|
192.168.1.10 | 3 | 2 | 1 |
192.168.1.21 | 1 | 0 | 1 |
192.168.1.35 | 30 | 25 | 5 |
192.168.1.48 | 3 | 2 | 1 |
192.168.1.72 | 8 | 6 | 2 |

La IP con mayor rango es **192.168.1.35**.

---

## 6.2 Desviación estándar (aproximada)

| IP | Desviación |
|----|-----------|
192.168.1.10 | baja |
192.168.1.21 | muy baja |
192.168.1.35 | media |
192.168.1.48 | baja |
192.168.1.72 | media |

---

## 6.3 Coeficiente de variación

El coeficiente de variación permite comparar la variabilidad relativa.

| IP | CV aproximado |
|----|--------------|
192.168.1.10 | bajo |
192.168.1.21 | bajo |
192.168.1.35 | medio |
192.168.1.48 | bajo |
192.168.1.72 | medio |

---

# 7. Funciones estadísticas básicas

Ejemplos en una hoja de cálculo:

**SUM**

Total de intentos por IP

```
=SUM(B2:F2)
```

**COUNT**

Número de registros

```
=COUNT(B2:F2)
```

**AVERAGE**

Media de accesos

```
=AVERAGE(B2:F2)
```

---

# 8. Funciones condicionales

Ejemplo para detectar IP sospechosas:

```
=IF(AVERAGE(B2:F2)>10,"Sospechoso","Normal")
```

Ejemplo para contar intentos altos:

```
=COUNTIF(B2:F2,">10")
```

---

# 9. Distribución de los datos

La mayoría de IPs presentan valores bajos de intentos fallidos.

Solo una IP presenta valores significativamente mayores.

Esto sugiere una **distribución con un outlier claro**.

---

# 10. Probabilidad del evento

Si se consideran 5 IPs y solo una presenta actividad anómala:

Probabilidad de ataque detectado:

```
1 / 5 = 0.20
```

Es decir:

**20 % de las IP analizadas muestran comportamiento sospechoso.**

---

# 11. Visualización de los datos

## Gráfico de intentos de acceso

```
Intentos de acceso fallidos (media)

IP1 ███
IP2 █
IP3 ███████████████████████████
IP4 ███
IP5 ███████
```

Interpretación:

La IP **192.168.1.35** muestra un volumen de intentos muy superior.

---

# 12. Interpretación del incidente

El análisis estadístico permite identificar:

- un **outlier claro**
- un patrón repetido de accesos
- actividad muy superior a la media

La IP **192.168.1.35** probablemente está ejecutando:

- un ataque de fuerza bruta
- un script automatizado

---

# 13. Recomendaciones de seguridad

A partir del análisis se recomienda:

- bloquear temporalmente la IP sospechosa
- aplicar autenticación multifactor
- limitar intentos de login
- monitorizar accesos por IP
- registrar actividad anómala en el SIEM

---

# 14. Conclusiones

La estadística permite detectar anomalías en datos de seguridad de forma rápida.

Las medidas de tendencia central ayudan a comprender el comportamiento general, mientras que las medidas de dispersión permiten detectar valores atípicos.

La visualización de datos facilita identificar incidentes de seguridad de forma inmediata.

Este tipo de análisis constituye la base para sistemas avanzados de detección de intrusiones y análisis de comportamiento en ciberseguridad.

------------
------------


# Análisis estadístico de un incidente de seguridad con Python

Ejemplo práctico de **análisis de datos aplicado a ciberseguridad** utilizando Python.

Este laboratorio simula un posible **ataque de fuerza bruta** detectado a partir de intentos de acceso fallidos registrados por dirección IP.

El objetivo es aplicar **estadística básica y visualización de datos** para detectar anomalías.

---

# 1. Importar librerías

Ejecutar primero este bloque en Google Colab.

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

---

# 2. Crear dataset simulado

Generamos un conjunto de datos con intentos de acceso fallidos.

```python
data = {
    "IP": [
        "192.168.1.10",
        "192.168.1.21",
        "192.168.1.35",
        "192.168.1.48",
        "192.168.1.72"
    ],
    "Lunes": [2,1,25,3,6],
    "Martes": [3,1,28,2,7],
    "Miércoles": [2,0,30,3,8],
    "Jueves": [3,1,27,2,7],
    "Viernes": [2,0,29,3,6]
}

df = pd.DataFrame(data)

df
```

---

# 3. Calcular estadísticas básicas

Calculamos **media, mediana y desviación estándar**.

```python
stats = pd.DataFrame()

stats["IP"] = df["IP"]
stats["Media"] = df.iloc[:,1:].mean(axis=1)
stats["Mediana"] = df.iloc[:,1:].median(axis=1)
stats["Desviacion"] = df.iloc[:,1:].std(axis=1)
stats["Max"] = df.iloc[:,1:].max(axis=1)
stats["Min"] = df.iloc[:,1:].min(axis=1)
stats["Rango"] = stats["Max"] - stats["Min"]

stats
```

---

# 4. Detectar anomalías

Consideramos anomalía cuando la media supera cierto umbral.

```python
stats["Anomalia"] = stats["Media"] > 10

stats
```

Esto nos permitirá identificar IPs sospechosas.

---

# 5. Gráfico de barras

Comparación de media de accesos fallidos por IP.

```python
plt.figure(figsize=(8,4))

sns.barplot(x="IP", y="Media", data=stats)

plt.title("Media de intentos de acceso fallidos por IP")
plt.xticks(rotation=45)

plt.show()
```

---

# 6. Histograma

Distribución de intentos de acceso.

```python
values = df.iloc[:,1:].values.flatten()

plt.figure(figsize=(8,4))

plt.hist(values, bins=10)

plt.title("Distribución de intentos de acceso")
plt.xlabel("Intentos")
plt.ylabel("Frecuencia")

plt.show()
```

---

# 7. Boxplot (detección de outliers)

El boxplot permite identificar **valores atípicos**.

```python
plt.figure(figsize=(8,4))

sns.boxplot(data=df.iloc[:,1:])

plt.title("Boxplot de intentos de acceso")

plt.show()
```

---

# 8. Interpretación de resultados

A partir del análisis podemos observar:

- La IP **192.168.1.35** presenta un número de intentos fallidos muy superior al resto.
- Este comportamiento se identifica claramente en el gráfico de barras.
- El **boxplot muestra un valor atípico**, indicando un posible incidente de seguridad.
- Este patrón podría corresponder a un **ataque automatizado de fuerza bruta**.
