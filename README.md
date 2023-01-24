# Documentacion para el proyecto de calificacion de propiedades

En el presente proyecto de machine learning se implementa un modelo de clasificación con aprendizaje supervisado que permite clasificar como bajo o no el precio de las propiedades en venta, utilizando los datos provistos.​

Dentro del dataset provisto se encuentran un train.parquet con datos para crear y entrenar el modelo, y un test.parquet que carece de los precios de las propiedades, y con el cual se realiza la clasificacion con el modelo propuesto.

## 1 Análisis de los datos y transformacion
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

En el archivo transformation.ipynb se encuentran las transformaciones que se le hicieron a la data para poder trabajarla con el modelo de machine learning. A continuacion una explicacion de estos cambios que se realizaron y el por que de las mismas
### 1.1 variables descartadas
Se descartaron todas las columnas description, id y todas las URLs (url, region_url, image_url),  por considerar que no aportan informacion relevante  al modelo y no estar relacionadas con el precio de las propiedades.
Tambien se descartaron las viariables de latitud y longitud, por considerar que no aportarian mejor informacion que las variables de region y state que ya sirven para hacerse una idea de los precios por zonas geograficas.
### 1.2 variables modificadas
Se decidio concatenar los valores de las columnas region y state (y posteriormente se eliminaron estas dos) en una nueva columna location, para que le sirvan al modelo a calcular el precio segun la ubicacion. La razon para concatenarlas es para evitar confuciones con regiones con el mismo nombre pero de diferente estado, y la columna state se elimina por que la region ya ofrece un area mas especifica.
### 1.3 variables dummies
Las variables de texto que se conservaron para el modelo (type, laundry_options, parking_options y location), se remplazaron por variables dummies numericas, paraque sean entradas validas para el modelo. A medida que se iban creando las dummies de cada columna, se comprobaba que no se generaran variables con correlacion perfecta entre si.

## 2 Modelo de aprendisaje
Para la solucion se escogio el modelo de arbol de decisiones, las pruebas para establecer el modelo y la profundidad a utilizar se encuentran en el archivo treeModel.ipynb. Para esto se hizo una divicion del dataset en un 75% para el training y un 25% para el testing.
### 2.1 Eleccion de la profundidad
Para elegir una profundidad del arbol optima, Se realizaron pruebas con profundidades de 1 a 99, calculando el accuracy y el recall score de cada prueba, primero con la columna objetivo del training y luego con la del testing y asi poder graficar ambos.

![enter image description here](/_src/accuracy.png)


![enter image description here](/_src/recall_score.png)

En las graficas se puede observar que los puntajes del test se separan cada vez mas con profundidades mayores a 35, por lo que se eligio esta como la profundudad para usar en el modelo de arbol de deciciones.
Como resultado el mdelo arrojo un promedio de accuracy de  0.86, y recall de  0.85.