### Análisis del turismo en Canarias 

## Introducción

El turismo es uno de los principales sectores económicos de Canarias, por lo que resulta de interés analizar su evolución y las diferencias existentes entre las distintas islas. El objetivo de este proyecto es estudiar el comportamiento del turismo en Canarias a partir de indicadores como el número de viajeros, las pernoctaciones y la estancia media. El análisis se centrará principalmente en tres aspectos: evolución temporal del turismo, diferencias entre islas y comportamiento de los principales mercados emisores.

## Fuente de datos

Los datos utilizados proceden del Instituto Canario de Estadística (ISTAC), organismo oficial de estadística de Canarias.

El conjunto de datos incluye información anual y mensual para distintos ámbitos territoriales, desde el conjunto de Canarias hasta islas y microdestinos turísticos. También contiene diferentes niveles de agregación por nacionalidad e indicadores como pernoctaciones, viajeros alojados, viajeros entrados y estancia media.

El enlace directo a la fuente de datos es el siguiente:

https://datos.gob.es/es/catalogo/a05003423-pernoctaciones-viajeros-alojados-y-entrados-y-estancia-media-segun-principales-nacionalidades-islas-y-microdestinos-de-canarias-por-periodos?


## Preparación y limpieza de los datos

La preparación de los datos se realiza en Python utilizando Pandas y NumPy. El dataset original contenía 369720 registros y 15 columnas. Para este análisis se conservan únicamente las variables de interés:

- `MEDIDA`: indicador turístico analizado.
- `TERRITORIO`: ámbito geográfico.
- `FECHA`: periodo temporal.
- `NACIONALIDAD`: procedencia de los viajeros.
- `OBS_VALUE`: valor de la observación.
  
Y se seleccionan los parámetros viajeros entrados, pernoctaciones y estancia media. También se limitan los territorios al conjunto de Canarias y a las siete islas principales.

Se crea una variable TIPO_FECHA para distinguir entre observaciones mensuales y anuales y facilitar el posterior análisis.

Se revisan los valores ausentes y se eliminan aquellas observaciones de OBS_VALUE sin valor disponible. No hay registros duplicados. 

También se revisan posibles valores atípicos. Por ejemplo, se detectó una estancia media de 91 días para turistas daneses en La Gomera en agosto de 2024. Al comprobar los datos asociados, se observa que corresponde a un único viajero con 91 pernoctaciones. Es un valor coherente con el dato original, aunque poco representativo. No se realizan modificaciones. 

## Análisis y visualización en Tableau

Una vez preparado el dataset, se importan los datos en Tableau para realizar el análisis visual. 

# ¿Cómo ha evolucionado el número total de viajeros que llegan a Canarias a lo largo de los años?

Se analiza la evolución del número total de viajeros entrados en Canarias entre 2018 y 2025. Para calcular el total se consideran conjuntamente los viajeros procedentes de España y la categoría `Mundo (excluida España)`. La evolución muestra una fuerte caída en 2020 (coincidiendo con la pandemia de COVID-19), seguida de una recuperación progresiva a partir de 2021. En 2023 el número de viajeros ya supera los niveles anteriores a 2020 y continúa aumentando durante 2024 y 2025. El crecimiento acumulado entre 2018 y 2025 es de aproximadamente un 6.0% (13.46 millones de viajeros frente a 14.27 millones, respectivamente).

# ¿Cómo ha evolucionado el número total de viajeros que llegan a cada una de las islas a lo largo de los años?

Se compara la evolución del número de viajeros entrados en las siete islas entre 2018 y 2025. La visualización permite observar tanto el impacto de la caída registrada en 2020 como las diferencias en la recuperación posterior entre territorios. Tenerife y Gran Canaria concentran durante todo el periodo el mayor volumen de viajeros. Fuerteventura y Lanzarote conforman un segundo grupo con cifras claramente inferiores, mientras que La Palma, La Gomera y El Hierro presentan volúmenes mucho menores.

# ¿Cúal es la variación porcentual entre los turistas registrados en 2018 y 2025 para cada isla?

Para comparar el nivel turístico previo a la pandemia con la situación de 2025, se calculó la variación porcentual del número de viajeros entrados entre ambos años. Para ello se creó en Tableau un campo calculado:

```text
(
    SUM(IF [Fecha] = "2025" THEN [Obs Value] END)
    -
    SUM(IF [Fecha] = "2018" THEN [Obs Value] END)
)
/
SUM(IF [Fecha] = "2018" THEN [Obs Value] END)
* 100
```

Los resultados muestran diferencias importantes entre islas. El Hierro presenta el mayor crecimiento relativo, con un 50,1 %, aunque parte de un volumen de viajeros considerablemente menor que las islas principales. Le siguen Tenerife, con un 11,9 %, y Fuerteventura, con un 10,3 %. En cambio, La Palma (-16,9 %) y La Gomera (-28,5 %) permanecen por debajo de los niveles registrados en 2018.

# ¿Cómo han evolucionado los principales mercados emisores entre 2025 y 2026?

Se explora la evolución mensual de los principales mercados emisores entre enero de 2025 y julio de 2026. Dado que el campo original de fecha contiene los valores mensuales en formato texto, se crea un campo calculado que permita asociar el valor con una fecha y así, poder visualizar la evolución en orden cronológico:

```text
MAKEDATE(
    INT(RIGHT([Fecha], 4)),
    INT(LEFT([Fecha], 2)),
    1
)
```

Se detecta, además, que no todas las nacionalidades disponen de información mensual. Por ejemplo, Irlanda, Italia, Noruega, Polonia y Suiza cuentan con datos anuales, pero no mensuales, lo que impide realizar una comparación homogénea entre todos los mercados.

Reino Unido es el principal mercado emisor, seguido de España. El tercer puesto corresponde a "Otros países o territorios del mundo (excluida España)", lo que limita la interpretación, ya que agrupa un volumen relevante de viajeros sin desglosar su procedencia. Sin embargo, esta limitación viene determinada por la fuente original y no puede resolverse con el dataset disponible.

# ¿Cuáles eran los principales mercados emisores en 2025?

Para identificar los principales mercados emisores se construye un gráfico de barras horizontales con el número de viajeros entrados en Canarias durante 2025, ordenado de mayor a menor por número de turistas y limitado al top 6. 

En 2025, el Reino Unido es el principal mercado emisor hacia Canarias, con aproximadamente 4.65 millones de viajeros. Le siguen España, con 2.70 millones, y Alemania, con 1.90 millones. Estos resultados muestran el elevado peso del mercado británico en el turismo canario, ya que representa con diferencia el principal origen de visitantes.

# ¿Cómo evoluciona el número total de pernoctaciones por isla entre 2018 y 2025?

Para analizar la evolución de las pernoctaciones se construye un gráfico de líneas que muestra el número total de pernoctaciones registradas anualmente en cada isla entre 2018 y 2025.
La serie muestra una fuerte caída generalizada en 2020, seguida de una recuperación progresiva a partir de 2021. Tenerife y Gran Canaria concentran durante todo el periodo el mayor volumen de pernoctaciones (>28 millones), seguidas de Lanzarote y Fuerteventura (entre 16-18 millones). La Palma, La Gomera y El Hierro presentan volúmenes considerablemente menores (<1.2 millones).

# ¿Cómo evoluciona la estancia media por isla entre 2018 y 2025?

Para calcular la estancia media global por isla se crea un campo calculado (estancia media = pernoctaciones totales / viajeros totales): 

```text
SUM(
    IF [Medida] = "Pernoctaciones"
    AND ([Nacionalidad] = "España"
         OR [Nacionalidad] = "Mundo (excluida España)")
    THEN [Obs Value]
    END
)
/
SUM(
    IF [Medida] = "Viajeros entrados"
    AND ([Nacionalidad] = "España"
         OR [Nacionalidad] = "Mundo (excluida España)")
    THEN [Obs Value]
    END
)
```

La estancia media presenta diferencias importantes entre islas. En 2025, Fuerteventura y Lanzarote registran las estancias medias más elevadas, mientras que Tenerife, a pesar de concentrar el mayor número de pernoctaciones, presenta una estancia media inferior. Esta diferencia se explica porque el número total de pernoctaciones depende tanto del volumen de viajeros como de la duración de sus estancias. Tenerife recibe un número de viajeros considerablemente mayor, mientras que en Fuerteventura y Lanzarote los visitantes permanecen más días de media. 

Además, La Gomera es la única isla cuya estancia media en 2025 supera la registrada en 2018, mientras que en el resto de islas se observa una reducción respecto al inicio del periodo analizado.

# ¿Cómo se compara la estancia media entre turistas nacionales y extranjeros entre 2018 y 2025? 

Se compara la evolución de la estancia media de los turistas procedentes de España con la de los turistas extranjeros entre 2018 y 2025.

Durante todo el periodo analizado, la estancia media del turismo extranjero es claramente superior a la del turismo nacional. En 2025, los turistas procedentes de España permanecen de media 4.1 días, frente a 7.6 días de los turistas extranjeros. Ambas series muestran una evolución relativamente estable.

## Diseño del dashboard final en Tableau

Tras completar el análisis exploratorio se diseña el dashboard final que resume los principales resultados de este análisis. Para ello, el dashboard se estructura en tres niveles: 
- KPIs generales, con el número total de turistas en 2025, la variación respecto a 2018, la isla con mayor volumen turístico y el principal mercado emisor (creamos hojas individuales para cada uno de ellos).
- Visualizaciones generales, con la evolución anual del turismo en Canarias y la comparación de las siete islas.
- Visualizaciones de detalle, centradas en los principales mercados emisores y en la estancia media según procedencia.

Para mejorar la interactividad, se crea un parámetro denominado Territorio seleccionado, que permite elegir entre Canarias y cualquiera de las siete islas. A partir de este parámetro se crea el siguiente campo calculado:

```text
[Territorio] = [Territorio seleccionado]
```

Este campo se utiliza como filtro en las visualizaciones de mercados emisores y estancia media. De esta forma, el usuario mantiene una visión general de Canarias en los gráficos superiores y puede explorar en detalle el comportamiento de cada territorio en las visualizaciones inferiores.

## Principales conclusiones

- Se observa claramente el impacto de la pandemia de COVID-19 sobre el turismo en 2020 y la posterior recuperación.
- En 2025, el número total de viajeros en Canarias supera aproximadamente en un 6 % el nivel de 2018.
- Tenerife y Gran Canaria concentran la mayor parte del volumen turístico del archipiélago.
- Reino Unido es el principal mercado emisor, seguido de España y Alemania.
- Los turistas extranjeros presentan una estancia media notablemente superior a la del turismo nacional.




