![image](https://github.com/user-attachments/assets/f2930855-29b0-4805-bc7f-5b7c17ba3b74)# ANÁLISIS DE PRECIOS Y VOLUMEN DE AGUACATE VENDIDOS EN EEUU Y PREDICCIÓN DEL PRECIO
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
El dataset no contiene datos vacíos, pero si tiene datos que son 0. Para poder ver con una visión más amplia de cuantos datos estamos hablando, he utilizado herramientas de visualización para generar la siguiente visualización:

![image](https://github.com/user-attachments/assets/b157ace7-a414-4311-ad55-6d8c2397c864)

Se observa como en XLarge Bags nos encontramos con muchísimos datos que son 0. También participan, aunque en menor medida, las características 4046, 4225, 4770, Large Bags, Small Bags, y Total Bags.
Vemos también una columna llamada Unnamed. 
 
![image](https://github.com/user-attachments/assets/7ddb0b02-9198-49e0-a7af-41f687bf14f5)


Podemos ver que es un simple índice, por lo que podemos prescindir de él.

Llegados a este punto, primero vamos a comprobar que no hayan fechas repetidas. Es importante que esta columna sobre todo esté en correcto formato ya que es nuestro indicador del tiempo. 
Para realizar esta comprobación, utilizaremos Pandas para aplicar una máscara booleana para comprobar filtrar los valores que sean duplicados. Debemos separar los datos por tipo y por región, ya que cada tipo tiene su propia fecha y entre regiones se van repitiendo también las fechas. Una vez agrupados, aplicaremos el método .transform() con una función lambda que llama al método duplicated que aplica sobre la serie Date de cada grupo y .transform() devuelve una serie booleana con el mismo índice.
 
![image](https://github.com/user-attachments/assets/4c9e37e9-7909-4bda-9de7-6025be6c3980)
![image](https://github.com/user-attachments/assets/f22b225d-350b-4cac-b314-8304edc955e1)

Para tratar los datos completados con 0 de las columnas mencionadas es necesario entender a que se deben estos valores, es decir, identificar porque son 0. Esto puede dar lugar a confusión ya que no siempre que los datos sean 0 significa erróneo, podría significar también que la cantidad es tan insignificante que se redondea a 0.
He decidido comprobar si la suma de los diferentes tipos de bolsa da como resultado “Total Bags”, de este modo si hay una diferencia muy alta en los casos donde XLarge o cualquier otra sea 0 quiere decir que los datos que son 0 son datos faltantes, de lo contrario se da por hecho que no se ha comercializado con este producto en las zonas y no se tiene en cuenta.
Al ejecutar el código me encuentro con varios casos en los que no es igual, pero al aplicar un error +-1, no se encuentra ningún resultado erróneo. Aplicamos este error por tema de redondeos, que tenga un márgen.

![image](https://github.com/user-attachments/assets/914410be-5e85-47b0-85c2-d2ba25b7582c)

### COMPLETANDO LOS VALORES “0”
Comprobamos cuántos 0 hay de las Bags de orgánicos y cuantos en convencional.

![image](https://github.com/user-attachments/assets/62a3511a-9c2a-448e-bd13-4f93ca1f68fc)

Para tratar estos datos lo voy a enfocar de la siguiente manera:

-	Agrupar por tipo y por región

-	Crear una nueva columna que será la Fecha Ordinal, que es una variable puramente numérica que indica los días que han pasado desde 01/01/0001. Esto ayudará a nuestro modelo a realizar mejores análisis.
 

-	Se entrenará un modelo por cada tipo de aguacate y región.

-	Si del tipo de aguacate la región tiene un 50% de datos que son 0, no se generará ningún modelo y los datos se quedarán como están.

-	De lo contrario, se generará un modelo de regresión lineal que servirá para rellenar los datos que son 0 con el valor predicho, aunque no sea muy preciso ni dependa de la estacionalidad nos será útil para hacer cálculos más aproximados.

-	Los datos que se rellenen, se sumarán a Total Bags y a Total Volume.

Para este procedimiento no he separado los datos en entrenamiento y prueba puesto que es una regresión lineal simple y esto como mucho nos va a indicar la tendencia, son valores muy aproximados.

 
Una vez tenemos el modelo preparado, podemos indicarle de qué región región queremos graficar el modelo y las predicciones para los valores 0. Por ejemplo podemos ver el caso de PhoenixTucson.

![image](https://github.com/user-attachments/assets/e827d8fc-7694-46e7-848e-ea78acaff6d0)


O California, que podemos observar como con el paso del tiempo la gente ha perdido interés por las bolsas grandes del tipo orgánicos. 

![image](https://github.com/user-attachments/assets/267fb481-ee88-42d9-a0e3-f4c7721676d6)

Al hacer esto, me doy cuenta que esto influirá en los datos de manera muy significativa, y no necesariamente a mejor. Si es cierto que en algunos casos la predicción no es tan mala, pero por ejemplo en california, al rellenar las bolsas vendidas con una regresión lineal nos da lugar a muchas confusiones y altera la veracidad de los datos. 

 
#### PROPHET Y KNN

Visto que las predicciones anteriores no han sido las más acertadas, decido probar un par de modelos para ver si vale la pena rellenar estos 0.
Aunque el volumen total si que aumenta estacionariamente, los valores separados como Large Bags no necesariamente sigue ese patrón. Por lo que las predicciones de Porphet no han sido muy útiles, ya que tergiversaron bastante los datos de manera probablemente errónea.

Siguiendo con los ejemplos del caso anterior, para PhoenixTucson vemos que prejudice mejor que el modelo de regresión lineal pero continua sin ser lo suficientemente útil, ya que nos tergiversa mucho los datos y nos puede perjudicar demasiado, ya que en casos de regiones en los que tengan muchos datos vacíos, se completarán no de manera muy precisa y esto nos puede llevar a conclusiones erróneas. Este modelo tiene un R^2 de 0.75, lo que no es malo del todo pero de nuevo, tampoco óptimo.

![image](https://github.com/user-attachments/assets/e3f924ad-5429-41ab-af26-857e3cece480)

Observamos que aunque no es mal del todo, en muchas ocasiones nos causará más problemas que información aportada. Con los orgánicos de California vemos que sigue una tendencia algo más clara, y aunque en el final del periodo 2018 decae bastante, mi modelo sigue prediciendo el patrón estacional. Este modelo tiene un R^2 calculado a partir de todos los datos existentes en Large Bags, no está separado para que el modelo se nutra de todos los datos existentes para rellenar los faltantes. Este modelo tiene R^2 de 0.35.

![image](https://github.com/user-attachments/assets/42d03740-bbcc-4eb5-94f6-7d7b20340b42)

Dado a este problema, decidí hacerlo con KNN estacional, que compara los vecinos más cercanos también de las otras fechas parecidas y aunque pueda parecer mejor, no es tampoco algo que se pueda implementar, al menos no para todas las regiones, para dependiendo que regiones funciona mejor o peor.
Continuando con los ejemplos anteriores, podemos ver PhoenixTucson:

![image](https://github.com/user-attachments/assets/8841cc54-48ec-43e5-a718-6f5e54f3b8dc)

Para California:

![image](https://github.com/user-attachments/assets/3f53fcab-b62f-4efc-af90-ae92f31880b4)

Pero de nuevo, estas predicciones no resultan muy útiles ya que aunque salieran bien, se sumaría X cantidad a cada región y todo se equilibraría de nuevo. 

Por lo que después de realizar estas pruebas decido dejar los datos en 0 tal y como estan..

Primeramente separaremos los datos en los dos tipos organic y conventional.
Asignaremos a cada region una "Rregion" que son 13.
Observamos las 4 regiones con la diferencia de picos mas altos
![image](https://github.com/user-attachments/assets/43f839b4-de29-4eb7-bf9d-27c3e5455f16)

![image](https://github.com/user-attachments/assets/fa660252-c2e4-41c2-a379-8c63a862e082)

![image](https://github.com/user-attachments/assets/06b34426-c4fb-4bbe-99e7-0cb3f2550137)



 ## Bibliografia
 https://documentation.avaya.com/es-XL/bundle/AvayaCMSDBItemsCalculations_r21/page/Avaya_Documentation_Center_navigation.html
