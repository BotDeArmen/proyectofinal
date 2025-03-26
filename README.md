# Emergency Call Predict: Prediccion de llamadas de emergencia al SEM utilizando IA y Mineria de Datos.
## Introducción
Este proyecto tiene como objetivo analizar y extrear insight clave a partir de los datos relacionados con las llamadas telefónicas recibidas por el SEM, el Sistema de Emergencias Médicas de Cataluña. A través de este estudio se busca comprender el comportamiento de la población y buscar patrones que nos permitan predecir la cantidad de llamadas que se recibirá en el futuro.
## Conjunto de datos
El análisis se basa en una base de datos que contiene un registro con la información relacionada a la llamada, pero no contiene información de la propia llamada; no sabemos la causa de estas llamadas.
  - Número de registros: muchos
  - Número de columnas: 85
  - Columnas principales:

Utilizaré un archivo que tiene el propio Avaya en su pagina que se encarga de explicar a que se refiere cada columna, ya que son los que gestionan esta base de datos.

## Objetivos

## Tecnología y herramientas

## Proceso diario y como evoluciona el trabajo
Este conjunto de datos posee muchos registros, lo cual es bastante bueno ya que no nos quedaremos cortos a la hora de entrenar el modelo de IA, pero también contiene muchas características. Antes de analizar cada caracterísitca, he hecho una visualización que nos permite ver que características contienen información y cuales no, de este modo si localizamos una característica que no contenga información, la podemos obviar. Con la siguiente linea de codigo generaremos un mapa de calor, donde los datos vacios se grafican de color rojo. 
![image](https://github.com/user-attachments/assets/52d2b79e-8b0b-4271-8556-dba5f8ce9ca4) \n
![image](https://github.com/user-attachments/assets/b292d681-6001-4026-9229-4a3e7e01b850) \n
Como vemos hay muchas que están vacias, lo que nos permite eliminar una buena cantidad de columnas que son irrelevantes ya que sus datos son nulos.


 ## Bibliografia
 https://documentation.avaya.com/es-XL/bundle/AvayaCMSDBItemsCalculations_r21/page/Avaya_Documentation_Center_navigation.html
