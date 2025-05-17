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

#### Regresión lineal
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

### ANÁLISIS DE VOLUMEN

Primeramente separaremos los datos en los dos tipos organic y conventional.
Asignaremos a cada region una "Rregion" que son 13.
Observamos las 4 regiones con la diferencia de picos mas altos
![image](https://github.com/user-attachments/assets/43f839b4-de29-4eb7-bf9d-27c3e5455f16)

![image](https://github.com/user-attachments/assets/fa660252-c2e4-41c2-a379-8c63a862e082)

![image](https://github.com/user-attachments/assets/06b34426-c4fb-4bbe-99e7-0cb3f2550137)

Los aguacates de tipo convencional se observa bastante bien un comportamiento estacionario, tiene picos muy definidos en ciertos momentos y valles muy definidos también. Aunque el volumen en sí varía mucho, es un patrón constante a lo largo de los años y se mueve entre los mismos máximos y mínimos.

![image](https://github.com/user-attachments/assets/e3da7748-22c2-4495-97e3-b75c15d9cf87)

Por lo contrario vemos una tendencia muy diferente. Si es cierto que los picos y los valles coinciden, pero los máximos y mínimos de los orgánicos han variado mucho desde que se tienen registros, lo cual indica un aumento de la popularidad de este producto.

![image](https://github.com/user-attachments/assets/8caefe07-f411-4ddd-8c95-4e7bc3990dd5)

En ambos gráficos observamos una gran coincidencia en el patrón de comportamiento. A principios de año, aproximadamente en Febrero, vemos un pico. Volvemos a observar repuntes a medida que se acerca el mes de Mayo. Indagando la razón de este consumo elevado, me encuentro que en Febrero se celebra la Super Bowl, un evento de rugby muy popular en EEUU y es muy probable que sea debido a esto. En Mayo observamos otro evento y es que es el día de la cultura mexicoamericana, por lo que sería lógico deducir que el consumo es debido a esta celebración, ya que es México es el principal proveedor de aguacates de EEUU. 

Para entender mejor cómo están separados los datos y poder hacer el análisis correctamente debemos observar nuestra característica Region, ya que no es lo que parece. Observamos como que hay una jerarquía, por lo que he creado una columna llamada clasificación en el que las ciudades que pertenecen a regiones amplias hereden el nombre de la región amplia, por comodidad.

Serie temporal Total Volume por región amplia / Convencional.
-	Semanal

![image](https://github.com/user-attachments/assets/40b71864-a94d-4fcf-820e-f2d4af69d8e0)

-	Bisemanal, suavizado media movil 6p.

![image](https://github.com/user-attachments/assets/ca3bac75-e617-46e9-a106-a18d427322a2)

En el gráfico anterior se observa claramente cómo hay ciertos eventos anuales que acentúan el consumo de aguacate. Los volúmenes tan dispares entre ciertas zonas es debido a la población que las habitan, ya que en WEST por ejemplo hay más densidad de población que en PLAINS. Observamos también volúmenes bastante elevados, pero siempre se mantiene en la misma linea y no hay un mayor consumo de un año a otro, en algunos casos los picos son más acentuados pero generalmente se mantiene estable.

Serie temporal Total Volume por región amplia / Organic.
-	Semanal

![image](https://github.com/user-attachments/assets/fb819b96-792d-4fdb-82bc-c19462f0c855)

-	Bisemanal, grafico suavizado media movil 6p.

![image](https://github.com/user-attachments/assets/7e306b15-8c19-46d8-88be-5f6117edd14e)

En cambio en los aguacates orgánicos se da un caso algo mas diferente. Observamos como en la mayoría de las regiones se ha ido popularizando el aguacate orgánico ya que el volumen total tiene una tendencia a la alza. Esto indica que la población se ha ido interesando acerca de la calidad de los alimentos y optan por las variedades más saludables, también porque no utilizan pesticidas, tienen más tacto con el medioambiente e incluso el sabor es diferente.

En ambos casos vemos que al agrupar los datos bisemanalmente no se pierde información de la tendencia y se ve más legible, por lo que utilizaremos esta agrupación para el análisis.

#### BOXPLOTS Y VIOLINS
Con los siguientes boxplots vemos la distribución de los datos. En la zona rectangular es donde se encuentran la mayoría de los datos y con los bigotes nos indica un rango donde hay datos pero en menor medida. Esto quiere decir que a más largos los bigotes, mayor dispersión de los datos. Con los puntos blancos nos indica que hay valores que se salen del rango y los considera outliers. 
 
![image](https://github.com/user-attachments/assets/2389bc60-c0ac-4150-9021-ab8d3d0c2850)
![image](https://github.com/user-attachments/assets/86e6ac7a-2233-44dc-9158-3cc993a3c4c6)

Para estar seguros que realmente estos puntos son Outliers (casos específicos), lo haremos utilizando las gráficas de violines. Estas gráficas nos permiten entender de una forma más sencilla cómo están distribuidos los datos, ya que estos “Outliers” si son muchos, se tendrían que tener en cuenta.

![image](https://github.com/user-attachments/assets/95b1d61d-ecee-43f3-b5fc-ba06f1600fb0)
![image](https://github.com/user-attachments/assets/6377b68a-3bd9-4f10-b619-a515fbffe384)
 
Para tomar la decisión correcta, voy a sacar la fecha de estos Outliers para ver si coincide con festividades. Los outliers se calculan con IQR, restamos el tercer cuartil (el valor en el que por debajo están el 75% de los datos) al cuartil 1 (25%) Q3-Q1= IQR 1.5 * IQR. 

![image](https://github.com/user-attachments/assets/bfcef2f1-bc6c-41b9-a26e-56e70c6dbd41)
![image](https://github.com/user-attachments/assets/59fea1ee-8310-4c10-b438-4bbfe6d1c343)

En la mayoría de los casos coinciden con fechas señaladas. 
En febrero se celebra la Super Bowl, un evento que dispara el consumo de aguacates. Los meses de marzo se consideran efecto de la Super Bowl también. 
El mes de agosto si que es extraños en el caso de South Central, por lo que lo regulamos al máximo permitido

![image](https://github.com/user-attachments/assets/9cd332f9-4191-42ed-a23b-6b1c8dd2c0f1)
 
Si observamos el boxplot de GreatLakes podemos ver como los orgánicos se han popularizado a lo largo de los años y esto ocurre en otras regiones también.

![image](https://github.com/user-attachments/assets/be97271a-729b-4aeb-930e-6ab6ba2c8ada)
![image](https://github.com/user-attachments/assets/4efd83d1-f785-4252-9c0d-d83c4c0704d3)

### ANÁLISIS DE PRECIOS
**HISTOGRAMA**

![image](https://github.com/user-attachments/assets/42717f3f-55d5-4c3a-9700-04b9531f7d1e)

Observamos que el precio en los aguacates orgánicos se mueve en rangos más elevados. Esto se debe a que requieren condiciones especiales para ser cultivados,
lo cual aumenta su valor.
En este tipo de gráficas observaremos siempre una campana de gaus, que nos indica que hay más precios registrados en la media.

- Tendencia convencional / Bisemanal / Región amplia

![image](https://github.com/user-attachments/assets/376216c1-d16f-4245-8b5a-2826234e5c41)

- Tendencia organico / Bisemanal / Región amplia

![image](https://github.com/user-attachments/assets/3a0ffaa9-648c-41e7-bd57-592923cc8ce7)

En cuanto a los orgánicos nos fijamos que los precios se mantienen bastante estables. Podemos notar como los picos si que son más altos a medida que avanza el tiempo pero por lo general se vuelve a normalizar.

**MATRIZ DE CORRELACIÓN**
En la siguiente matriz de correlación podemos observar la correlación entre las características del dataframe. Para GreatLakes y el tipo convencional, se observa una correlación negativa entre Total Volume y Average Price. 

![image](https://github.com/user-attachments/assets/de42239f-c65f-4d7e-a5cb-19ec39f2b145)

Para demostrar esta correlación negativa, lo podemos graficar para ver mejor su evolución.

![image](https://github.com/user-attachments/assets/549af614-7340-45ec-9626-d473dfc6782d)

Observamos claramente cuándo Total Volume sube, AveragePrice baja.
Lo mismo ocurre para los aguacates orgánicos, pero vemos como el volumen total poco a poco tiene bajadas menos pronunciadas y cada vez están mas asentados en el mercado.

![image](https://github.com/user-attachments/assets/f4fa388a-e935-4993-a201-5769b9e96377)

Podemos ver otro ejemplo, California organic, donde podemos observar un patrón muy similar.

![image](https://github.com/user-attachments/assets/815c952e-3a91-4a85-8bb4-340ba21af447)

Cohorte de retención el primer mes es el que suepran su media, al segundo mes se le resta el valor del primer mes para ver la retención.

**Mapa de calor**
Observamos de color azul cuando el precio difiere negativamente del precio inicial y de colores más rojos cuando esta diferenicia es positiva.

![image](https://github.com/user-attachments/assets/a06cf648-b602-4f79-8849-e04bc1b70e83)

En este caso podemos ver la evolución en un gráfico de linea temporal

![image](https://github.com/user-attachments/assets/c1886e44-e941-40d7-9848-c9d457a9a9f5)

Vemos que en líneas generales, cuando una cohorte supera por primera vez su media el precio tiende a subir y luego caer por debajo. Esto se debe a que la subida de precio está relacionado con la comercialización en esas épocas del año. A medida que se acercan las etapas de mayor consumo, observamos un crecimiento mayor en el precio (son épocas de mucha demanda por lo que el precio sube)y bajan de nuevo y vuelven a subir. Vemos que las cohortes suelen generarse en momentos en los que el consumo era debido a que se acercaba alguna fecha señalada, por lo que su tendencia indica aumento en el momento en el que superó su media por primera vez. Una vez pasado este evento observamos una gran disminución en el precio, pero de nuevo volvemos a tener picos elevados durante la misma etapa. 
Si nos fijamos en el grupo 2015-05 podemos ver que su pico es el más alto, seguido de su vecino del mes 06. Del grupo nacido en 2016-06 se observa que tiene una retención extremadamente positiva, pero a finales de 2017 /inicios de 2018 vemos una caída.

### Total Volume
Estas son unas nuevas cohortes pero con Total Volume. Observamos como en los grupos de cohortes que nacen antes de 2016 tienen muchas variaciones en comparación a los grupos más recientes, en los que se observa una clara estabilidad

![image](https://github.com/user-attachments/assets/ed35ab81-4a61-4c2a-8a5f-5034086da393)

Si miramos el gráfico vemos esto bastante más claro.
Observamos que cuando hay más volumen de aguacates el precio tiende a bajar, ya que la demanda no es la suficiente. 

![image](https://github.com/user-attachments/assets/fadd8ede-9f97-461e-ad0d-fa315635734f)

La región perteneciente a la cohorte 2017-05, BaltimoreWashington, tiene un volumen bastante constante, si observamos la gráfica Volumen- Precio podemos observar cómo a partir de 2017-05 los valores de volumen sube, baja el precio.

![image](https://github.com/user-attachments/assets/c82c353b-2c77-4c8c-9d48-fad3340560cb)

En este otro gráfico he filtrado las cohortes más recientes para observar con más claridad su estabilidad.

![image](https://github.com/user-attachments/assets/18f993cd-72d6-417a-b82c-f026c1e7f3c1)

Podemos hacer recuento de la cantidad de cohortes que hay en cada grupo y por tipo. 
Para comenzar podemos ver que cuando se habla de estabilidad positiva nos encontramos muchos grupos más que son orgánicos que convencionales. 

![image](https://github.com/user-attachments/assets/afcf62a2-70a8-4822-a9df-f959f23c2a62)

Para verlo más claro, aquí extraigo las regiones y el tipo de los registros en los que las cohortes resultaron más estables. Se observa claramente un mayor asentamiento en el mercado por parte de los aguacates orgánicos, parece que los orgánicos que superan su media más tarde superan un umbral, lo que indica que su mercado ha tardado en madurar.

![image](https://github.com/user-attachments/assets/2eb5d540-6f86-465f-946b-9d28373aff23)

De la cohorte 2017-01, vemos como más allá de estabilizarse, el volumen consumido aumenta significativamente. Observamos que las regiones de aumento son pertenecientes a Northeast, como Philadelphia, Nueva York o HartfordSpringfield. De la siguiente cohorte, 2017-02, se observa que las regiones que predominan son Indianápolis, y GrandRapids, en Midwest. En 2017-03 volvemos a ver regiones de Northeast, como NorthernNewEngland o HarrisburgScranton. Y por último en 2017-05 observamos BaltimoreWashington, de Southeast.
 
Para ver entender mejor que está sucediendo en Northeast, podemos ver cuantas de sus regiones están dentro de las cohortes estables:

![image](https://github.com/user-attachments/assets/b72258e9-dc8f-456f-b791-6bad2e2d01a1)

Observamos que todas las regiones con tipo orgánico están dentro de las cohortes estables, lo que deducimos que es un mercado que se ha consolidado muy bien en esta zona. Los convencionales destacan por lo pronto que superan su media y por lo variable que es su consumo a lo largo del año.
Con las regiones de MidSouth y Southeast tenemos casos similares, aunque regiones como Roanoke o RichmondNorfolk son excepciones.

![image](https://github.com/user-attachments/assets/12876f88-62a6-4215-983a-0c3a8495a430)
![image](https://github.com/user-attachments/assets/8d904fa5-9c4e-49e6-83fd-2f3e08315e56)

### Análisis por regiones específicas

Para realizar análisis más específicos y modelos más precisos, es importante seleccionar regiones que sean representativas y que tengan comportamientos interesantes, intentando cubrir zonas diversas de EEUU.

**Northeast: Philadelphia**
Pertenece a un grupo de cohorte que tiene un comportamiento interesante, su total volume aumenta drásticamente después de superar su media (tipo orgánico)

**West: California**
Es un mercado enorme, a menudo un referente, y sus dinámicas pueden ser complejas y distintas a otras zonas. Es bueno tenerlo para representar la costa Oeste y un alto volumen.

**Southeast: Roanoke**
Es de los pocos orgánicos que supera su media en cohortes tempranas.

**Midsouth: Nashville**
Buena elección para representar la zona Midsouth. Además presenta un comportamiento interesante y es que su tipo convencional está dentro de las cohortes más recientes, es decir las que mantienen o aumentan su volumen.

**Midwest: Chicago**
Excelente para tener un gran mercado del Medio Oeste, con su propia dinámica.

EVOLUCIÓN - CALIFORNIA

![image](https://github.com/user-attachments/assets/3a65efa8-6fd5-477d-931d-b8792ababb4f) ![image](https://github.com/user-attachments/assets/84631cb2-34c4-4f72-acc4-e14c4174e0f8)

Se observa una clara estacionalidad en los datos y en el caso de orgánicos vemos una tendencia alcista.

EVOLUCIÓN - CHICAGO

![image](https://github.com/user-attachments/assets/76043e9c-dab0-48a8-a745-87d712485348) ![image](https://github.com/user-attachments/assets/532b3c5c-5129-49f6-851f-f2bfeb0d8805)

En chicago podemos observar una tendencia decreciente del volumen de convencionales y una creciente en organicos

EVOLUCIÓN - ROANOKE

![image](https://github.com/user-attachments/assets/d50946fa-dd5f-4112-8221-39c40e538b70)

EVOLUCIÓN PHILADELPHIA

![image](https://github.com/user-attachments/assets/6894b43e-5ba2-4245-8e20-670c00bddc66)

EVOLUCIÓN - NASHVILLE

![image](https://github.com/user-attachments/assets/8d4a1738-8dab-41b9-8102-60685d66a628)

Curiosamente en Nashville, aumentan las ventas de convencionales a lo largo del tiempo.

**Outliers**
Observamos bastantes outliers. En Roanoke en 2015 vimos un repunte muy alto sobre los productos orgánicos. Esto es muy probable que se deba a que la fundación de Roanoke Natural Foods Co-op celebró su 40 aniversario, realizando campañas de márketing para popularizar este tipo de productos. Al tratarse de una situación especial, también se considerarán como outliers y los toparemos según IQR

![image](https://github.com/user-attachments/assets/d6610199-3e2c-4cb5-b9f4-018b2be12120)

Si bien es cierto que en muchas ocasiones caen en eventos especiales, estos outliers no nos benefician en absoluto a la hora de generar el modelo. Con la misma técnica de IQR, cambiaremos estos valores para toparlos al Q3 + 1.5 *IQR.

Una vez topados, podemos observar con boxplot para ver si se ha realizado correctamente. 
**Datos sin topar**

![image](https://github.com/user-attachments/assets/110f1900-dcb7-4e85-88ca-f63c9c0a4263)

**Datos después de IQR**

![image](https://github.com/user-attachments/assets/73f29cb8-0fcf-4270-bc10-929d4fdb9191)

Estos outliers se han calculado de manera anual puesto que hacerlo de manera total la variación de los otros años puede influir mucho en los datos. 
Pongo de ejemplo únicamente esta región, pero se ha hecho lo mismo para todas las combinaciones. Al haber hecho los outliers por años, no podemos agruparlos todos en un único boxplot general, ya que datos de 2018 pueden ser outliers para los de 2015, entonces aparecen outliers sin serlo en realidad. Ejemplo:

![image](https://github.com/user-attachments/assets/3d808b08-d468-4410-aa5f-a1905408de93)

Vemos que en Philadelphia si aparecen outliers si agrupamos los años, aunque no sea el caso si hacemos una separación distinta.

### INGENIERIA DE CARACTERÍSTICAS
Para poder entrenar de manera más eficiente los modelos, primeramente he decidido agregar algunas características que puedan facilitarles identificar patrones.

![image](https://github.com/user-attachments/assets/1fb7dbdc-c667-490a-8ce4-8d9c5d8a99b4)

Observamos Lag 26 de Total Volume que nos indica la cantidad de volumen de hace 26 periodos. Lag 1 de Price nos indica el precio del periodo anterior. En los Near, se indica si se acerca esta festividad, es 1 mientras 4 periodos antes de la fecha y en los 2 siguientes.

Se aplican estos cambios al data frame original para que todos los datos sean uniformes. 

### MODELOS PREDICTIVOS
**REGRESIÓN LINEAL**
Generalmente utilizado con datos con tendencias claras y poca variabilidad en datos. No nos será de gran utilidad debido a la naturaleza del dataset.
Modelos bastante inútiles para estos casos, ya que los datos tienen comportamientos variados.

![image](https://github.com/user-attachments/assets/aa4900e4-dceb-4f2a-9e71-3983f4745d82)
 
**REGRESIÓN POLINÓMICA**
Para la regresión polinómica y la búsqueda de hiper parámetros he utilizado GridSearch. Se observa como el mejor resultado me lo proporciona un grado 1, lo que es una regresión lineal. La valoración del modelo se basa en el error cuadrático medio, que es el cálculo del error en las predicciones.
Resultados:
Mejor grado polinómico encontrado: 1

Métricas en el CONJUNTO DE PRUEBA DE INTERPOLACIÓN (<2018, 20%):
  RMSE: 55819.19
  R^2: -4.42

Métricas en el CONJUNTO DE EXTRAPOLACIÓN (2018):
  RMSE: 24165.40
  R^2: -0.39

De todos modos, podemos aplicar un polinomio de grado 3 para observar el comportamiento:
 Obtenemos como resultados 
Métricas en CONJUNTO DE PRUEBA DE INTERPOLACIÓN (<2018, 20%):
  RMSE: 44741.20
  R^2: -2.48

Métricas en CONJUNTO DE EXTRAPOLACIÓN (2018):
  RMSE: 211885.59
  R^2: -105.54

![image](https://github.com/user-attachments/assets/e27c40f5-d018-4682-ad73-428f8642b0fa)

RANDOM FOREST
Son modelos utilizados mayoritariamente para clasificación, por lo que no suele tener en cuenta tendencias. Este modelo trabaja creando varios árboles de decisión y estos árboles hacen cada una sus predicciones sobre el target. Una vez hecho esto, la predicción que más haya salido es la que Random Forest toma como válida.

![image](https://github.com/user-attachments/assets/479e2191-840e-4df1-ae22-72fb231acee8)

![image](https://github.com/user-attachments/assets/7559d8d5-22d8-4baf-80a8-562a18b1ea21)

 
Métricas de ajuste en CONJUNTO DE ENTRENAMIENTO DE INTERPOLACIÓN (<2018, 80%):
  RMSE (ajuste entrenamiento): 10683.64
  R^2 (ajuste entrenamiento): 0.94

Métricas en CONJUNTO DE PRUEBA DE INTERPOLACIÓN (<2018, 20%):
  RMSE: 29653.37
  R^2: -0.53

Métricas en CONJUNTO DE EXTRAPOLACIÓN (2018):
  RMSE: 17173.70
  R^2: 0.30
En el conjunto de datos de entrenamiento vemos que tiene un sobreajuste del 94%, pero en predicciones a futuro no se comporta muy bien.
 
Realizando GridSearch para reducir el sobreajuste con algunos parámetros esenciales como 
Max_depth (profundidad de cada árbol), min_samples_leaf (mínimo de muestras requeridas para separar un nodo) y 'min_samples_split' (mínimo de muestras requeridas en un nodo hoja.)
He aplicado un rango de 5 datos para cada parámetro y el que mejor encuentra es el siguiente.
Iniciando GridSearchCV para Random Forest (con 3-fold TimeSeriesSplit)...
Fitting 3 folds for each of 36 candidates, totalling 108 fits
Mejores parámetros encontrados:
{'max_depth': 3, 'min_samples_leaf': 5, 'min_samples_split': 40}

Métricas de ajuste del MEJOR MODELO en CONJUNTO DE ENTRENAMIENTO DE INTERPOLACIÓN:
  RMSE (ajuste entrenamiento): 31491.47
  R^2 (ajuste entrenamiento): 0.46

Métricas del MEJOR MODELO en CONJUNTO DE PRUEBA DE INTERPOLACIÓN (<2018, 20%):
  RMSE: 22490.08
  R^2: 0.12

Métricas del MEJOR MODELO en CONJUNTO DE EXTRAPOLACIÓN (2018):
  RMSE: 31565.13
  R^2: -1.36

Se ve claramente que reduce el sobreajuste, funciona algo mejor con los datos de prueba pero sigue siendo muy deficiente para predecir. 

SARIMA
Para la selección mencionada anteriormente utilizaré el modelo SARIMA, una extensión del modelo ARIMA que tiene en cuenta la estacionalidad. 

Para estos modelos estacionales es importante hacer la separación de datos de manera cronológica, ya que estos modelos trabajan mejor de esta manera. Observamos los datos de entrenamiento de color azul, los de prueba en naranja y las lineas discontinuas son las predicciones del modelo.
SARIMA necesita algunos parámetros de configuración para desempeñar el modelo. Para calcular dichos valores debemos observar las gráficas de Autocorrelación y la autocorrelación parcial. 
Autocorrelación se utiliza para ver el impacto del dato anterior sobre el actual. No se observa ninguna estacionalidad, vemos que decrece lentamente lo que no indica estacionalidad.
Estas gráficas se deben hacer para cada region-type ya que cada uno de ellos se comporta de manera diferente. En este caso he utilizado Philadelphia- Organic para llevar a cabo el estudio.

![image](https://github.com/user-attachments/assets/51878647-c800-4d0d-ac70-4f73b7e021d8)

Aplicando una diferencia no estacionaria “d=1” si que podemos observar una estacionalidad, lo que esto hace es, en vez de tener en cuenta el dato, tiene en cuenta la diferencia del dato actual con el dato anterior. En este caso sí que se observa una estacionalidad clara.

![image](https://github.com/user-attachments/assets/8ebda5f0-88ef-460d-b0d5-9a0a9d481dcc)

Aplicaré los parámetros encontrados sobre las combinaciones mencionadas para ver el comportamiento de SARIMA.

CALIFORNIA

![image](https://github.com/user-attachments/assets/5ef67bf4-d8de-45ee-9767-2afdfd6b6972) ![image](https://github.com/user-attachments/assets/b9a99c2e-40d3-4a0b-87d0-2cb9537da5bf)

CHICAGO

![image](https://github.com/user-attachments/assets/b8e91795-210e-4e25-a4f8-321a5cfd28ce) ![image](https://github.com/user-attachments/assets/cc37c6df-e0fb-4831-9d1c-f207d097913d)

ROANOKE - organic

![image](https://github.com/user-attachments/assets/6345e06f-71de-4d73-9d57-505bf6baca54)

PHILADELPHIA- organic

![image](https://github.com/user-attachments/assets/fc599cd7-cb02-4d6d-bb7e-245c54ff2919)

NASHVILLE - conventional

![image](https://github.com/user-attachments/assets/49dba8b9-e948-45c3-8c10-333217d9293f)

En algunos casos el modelo captura bien el comportamiento de los datos, mientras que en otros únicamente los mantiene en el rango de confianza.

### CONCLUSIONES

El objetivo principal fue identificar los factores que subyacen a las fluctuaciones de precios y volúmenes, así como evaluar la viabilidad de desarrollar modelos predictivos robustos.

Impacto Estacional y Eventos: El consumo aumenta notablemente con eventos como la Super Bowl y festividades de mayo, afectando volumen y precios.

Auge de Aguacates Orgánicos: Su popularidad y volumen de ventas crecen constantemente, a diferencia de los convencionales, más estables.

Relación Volumen-Precio Inversa: A mayor volumen de aguacates en el mercado, el precio tiende a bajar, y viceversa.

Mercado Regional Heterogéneo: Existen diferencias significativas en consumo, precios y preferencia por orgánicos entre regiones de EE. UU..

Modelos Clásicos Limitados: Regresión lineal y polinómica ofrecieron poca utilidad predictiva para este mercado. Random Forest tendió al sobreajuste en extrapolaciones.

SARIMA, Mejor Opción Estacional: Este modelo fue el más apto para capturar la estacionalidad en las series de volumen, requiriendo una cuidadosa parametrización.

Importancia de Ingeniería de Características: Crear variables como lags de volumen/precio y marcadores de festividades fue relevante para los modelos.

Análisis de Cohortes y Madurez del Mercado: Este análisis reveló una mayor estabilidad en cohortes recientes, especialmente de orgánicos, sugiriendo una consolidación de estos mercados.

 ## Bibliografia
 https://documentation.avaya.com/es-XL/bundle/AvayaCMSDBItemsCalculations_r21/page/Avaya_Documentation_Center_navigation.html
