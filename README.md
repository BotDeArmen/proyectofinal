# ANÁLISIS DE PRECIOS Y VOLUMEN DE AGUACATE VENDIDOS EN EEUU Y PREDICCIÓN DEL PRECIO
## Introducción
Aguacates, qué ricos verdad? A muchos les gustará y a otros no tanto, lo que está claro es que valen su peso en oro. Para ver la tendencia que sigue el tan llamado oro verde, utilizaremos un dataset de aguacates facilitados por el profesor Anthony. En este CSV tenemos diferentes columnas referentes a cantidades de aguacates vendidas por ciertas regiones o ciudades.
## Conjunto de datos
El análisis se basa en una base de datos que contiene un registro con la información relacionada a la llamada, pero no contiene información de la propia llamada; no sabemos la causa de estas llamadas.
  - Número de registros: muchos
  - Número de columnas: 13
    -  Date: Fecha del registro (semanal)
    -  AveragePrice: Precio promedio
    -  Total Volume: El volumen total
    -  4046: Tipo de aguacate Hass pequeño
    -  4225: Tipo de aguacate Hass grande
    -  4770: Tipo de aguacate Hass extragrande
    -  Total bags: Cantidad de bolsas vendidas totales
    -  Small Bags: Bolsas pequeñas
    -  XLarge Bags: Bolsas grandes
    -  type: Tipo
    -  year: Añi
    -  region: Lugar, ciudad.
  - Columnas principales:

Utilizaré un archivo que tiene el propio Avaya en su pagina que se encarga de explicar a que se refiere cada columna, ya que son los que gestionan esta base de datos.

## Objetivos

## Tecnología y herramientas

## Proceso diario y como evoluciona el trabajo
Este conjunto de datos posee muchos registros, lo cual es bastante bueno ya que no nos quedaremos cortos a la hora de entrenar el modelo de IA, pero también contiene muchas características. Antes de analizar cada caracterísitca, he hecho una visualización que nos permite ver que características contienen información y cuales no, de este modo si localizamos una característica que no contenga información, la podemos obviar. Con la siguiente linea de codigo generaremos un mapa de calor, donde los datos vacios se grafican de color rojo. 

![image](https://github.com/user-attachments/assets/d93a70c8-6fa6-41ee-8311-582bdaec856c)

Como vemos hay muchas que están vacias, lo que nos permite eliminar una buena cantidad de columnas que son irrelevantes ya que sus datos son nulos.


 ## Bibliografia
 https://documentation.avaya.com/es-XL/bundle/AvayaCMSDBItemsCalculations_r21/page/Avaya_Documentation_Center_navigation.html
