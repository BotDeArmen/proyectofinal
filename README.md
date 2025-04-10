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
Se limpian y cargan los datos: Se gestionan los valores nulos, se eliminan duplicados y se estandarizan los formatos.
Se exploran con gráficos iniciales: Se buscan patrones generales a través de visualizaciones básicas.
Se analiza la evolución en el tiempo: Se estudian precios y volúmenes promedio, con foco en la estacionalidad.
Se compara por regiones de EE. UU.: Se identifican diferencias en los patrones regionales.
Se evalúa entre tipos de aguacate: Se buscan las disparidades entre las variedades.
Se visualizan los hallazgos clave: Se crean gráficas para comunicar las conclusiones principales.
## Tecnología y herramientas

## Proceso
Primeramente separaremos los datos en los dos tipos organic y conventional.
Asignaremos a cada region una "Rregion" que son 13.
Observamos las 4 regiones con la diferencia de picos mas altos
![image](https://github.com/user-attachments/assets/43f839b4-de29-4eb7-bf9d-27c3e5455f16)

![image](https://github.com/user-attachments/assets/fa660252-c2e4-41c2-a379-8c63a862e082)

![image](https://github.com/user-attachments/assets/06b34426-c4fb-4bbe-99e7-0cb3f2550137)


 ## Bibliografia
 https://documentation.avaya.com/es-XL/bundle/AvayaCMSDBItemsCalculations_r21/page/Avaya_Documentation_Center_navigation.html
