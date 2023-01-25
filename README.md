# Documentacion para el proyecto de clasificacion de propiedades
En el presente proyecto de machine learning se implementa un modelo de clasificación con aprendizaje supervisado que permite clasificar como bajo o no el precio de las propiedades en venta, utilizando los datos provistos.​

Dentro del dataset provisto se encuentran un train.parquet con datos para crear y entrenar el modelo, y un test.parquet que carece de los precios de las propiedades, y sobre el cual se realiza la clasificacion con el modelo propuesto.

Antes de explicar el desarrollo del proyecto, una breve descripcion de las carpetas y archivos dentro del repositorio:
 - Carpeta **Datasets_original**: aca se encuentran la dataset provista para el proyecto (sin modificar)
- Carpeta **Datasets**: aca se guardo la data ya transformada (ver punto 1)
- Carpeta **tests_results**: aca se guardaron los resultados de las pruebas con distintas profundidades en el modelo de arbol de decisiones (ver punto 2)
- Archivo **transformation.ipynb**: aca se realizaron las transformaciones del dataset original (ver punto 1)
- Archivo **treeModel.ipynb** aca se llevaron a cabo las pruebas para el modelo de arbol de decisiones (ver punto 2)
- Archivo **testPrediction.ipynb**: aca se realizo la prediccion a la data provista en test.parquet


## 1. Análisis de los datos y transformacion
*Nota: el codigo de las transformaciones y la grafica descritas en este apartado se encuentra dentro del archivo* ***"transformation.ipynb"*** *y la data ya transformada se guardo en la carpeta "Datasets"*

En el dataset se encuentran las siguientes variables que describen las propiedades en venta:

| variable | tipo | descripcion |
|--|--|--|
id | int | id de la propiedad |
url | str | url del anuncio de venta |
region | str | region de en donde se encuentra la propiedad |
region_url | str | url a un mapa de la region |
price | int | precio de la ppropiedad |
type | str | tipo de propiedad (casa, apto...) |
sqfeet | int | metros cuadrados de la proiedad |
beds | int | numero de habitaciones que tiene la propiedad |
baths | float | numero de baños que tiene la propiedad |
cats_allowed | int | si se permiten gatos en la proiedad (1 ó 0)|
dogs_allowed | int | si se permiten perros en la proiedad (1 ó 0) |
smoking_allowed | int | si se permite fumar en la proiedad (1 ó 0) |
wheelchair_access | int | si hay acceso para sillas de ruedas(1 ó 0) |
electric_vehicle_charge | int | si hay puntos de carga para vehiculos electricos |
comes_furnished | int | si la propiedad esta amueblada |
laundry_options | str | las ociones de lavanderia |
parking_options | str | las opciones de parqueadero |
image_url | str | url a una imagen de la propiedad |
description | str | descripcion de la propiedad |
lat, long | float | latitud y longitud de la propiedad |
state | str | estado donde se encuentra la propiedad |

En la siguiente grafica se puede observar la correlacion entre las variables numericas de esta data inicial:

![enter image description here](/_src/heatmap.png)

Para poder desarrollar un modelo apropiado se tomo la decision de descartar y transformar determinadas variables, a continuacion una explicacion de estos cambios y del porque se decidieron manejar de esta forma:
### 1.1 variables descartadas
Se descartaron las columnas description, id y todas las URLs (url, region_url, image_url),  por considerar que no aportan informacion relevante  al modelo y no estar relacionadas con el precio de las propiedades.
Tambien se descartaron las viariables de latitud y longitud, por considerar que no aportarian mejor informacion que las variables de region y state que ya sirven para hacerse una idea de los precios por zonas geograficas.
### 1.2 variables modificadas
Se decidio concatenar los valores de las columnas region y state (y posteriormente se eliminaron estas dos) en una nueva columna location, para que le sirvan al modelo a calcular el precio segun la ubicacion. La razon para concatenarlas es para evitar confusiones con regiones con el mismo nombre pero de diferente estado, y la columna state se elimina porque la region ya ofrece un area mas especifica.
### 1.3 variables dummies
Las variables de texto que se conservaron para el modelo (type, laundry_options, parking_options y location), se remplazaron por variables dummies numericas, para que sean entradas validas para el modelo. A medida que se iban creando las dummies de cada columna, se comprobaba que no se generaran variables con correlacion perfecta entre si.

## 2. Modelo de aprendizaje
Para la solucion se escogio el modelo de arbol de decisiones, El codigo de las pruebas para establecer el modelo y la profundidad a utilizar se encuentran en el archivo **"treeModel.ipynb"**, y los resultados de las pruebas para elegir la profundidad se encuentran en **"tests_results/depth_test.csv"** y en  **"tests_results/depth_train.csv"**.
### 2.1 Eleccion de la profundidad
Para elegir el hiperparametro de profundidad del arbol optima, Se realizaron pruebas con profundidades de 1 a 199, calculando el accuracy y el recall score de cada prueba. Para estas pruebas se utilizo el dataset ya transformado en el punto 1, y se hizo una divicion del dataset en un 75% para el training y un 25% para el testing, se calcularon el accuracy y el recall score primero prediciendo la misma data del training y luego con la del testing, para asi poder graficar ambos y encontrar con que profundidad el modelo comiensa a memorizar la data del training

![enter image description here](/_src/accuracy.png)


![enter image description here](/_src/recall_score.png)

En las graficas se puede observar que los puntajes del test se separan cada vez mas con profundidades mayores a 60, lo que indica que comienza a memorizar la data del training, por lo que se eligio esta como la profundidad maxima para usar en el modelo de arbol de decisiones.

Una vez establecido el hiperparametro de la profundidad maxima se llevo a cabo la creacion del modelo. Como resultado de esta prueba se obtuvo un accuracy aproximado de 0.909, y recall aproximado de  0.898.