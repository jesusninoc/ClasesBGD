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
