# Practicas MongoDB
## _La muerte a collejas por lemonCode_

Para esta practica se nos solicita:

- Montar un contendor docker de mongo y restaurar una base de datos de airbnb
- Saca en una consulta cuántos apartamentos hay en España.
- Lista los 10 primeros 

## Requerimientos previos
- Creamos una imagen de docker y con run de paso el contenedor
>docker run --name my-mongo-db-airbnb -p 27017:27017 -d mongo:4.4.3

- Si no estuviese el contenedor corriendo ejecutamos un start
>docker start my-mongo-db-airbnb

- Copiamos la base de datos que nos hemos descargado al contenedor
>docker cp backup my-mongo-db-airbnb:/opt/app

- Nos conectamos en modo interactivo, chequeamos que están las bases de datos 
>docker exec -it my-mongo-db-airbnb
>cd ./opt/app
>ls
>listingsAndReviews.bson  listingsAndReviews.metadata.json

- sin salir del modo interactivo restauramos la base de datos de airbnb
>mongorestore --db listingsAndReviews .

## Parte Obligatoria

### General
En este base de datos puedes encontrar un montón de apartamentos y sus reviews, esto está sacado de hacer webscrapping.

Pregunta: Si montaras un sitio real, ¿qué posibles problemas pontenciales le ves a cómo está almacenada la información?

> LA gran cantidad de documentos que manejas. Para poder solentar esto, tenemos metos para trabajar la Paginación, como son:


    - skip()
    - limit()
    - min()
    - max ()
    - sort()

> Otra cosa que podemos hacer para gestionar el gran volumen de datos es crear indices, basados en la mayoria de consultas o mas comunes que se haran en nuestra bse de datos. Por ejemplo por ciudad y precio.
```
use('listingsAndReviews');
db.listingsAndReviews.ensureIndex( { "address.country" : 1, "price":-1 } );
```

### Consultas

#### Basico
- Saca en una consulta cuántos apartamentos hay en España.

```
use('listingsAndReviews');
db.listingsAndReviews.count({"address.country": "Spain"});
```

- Lista los 10 primeros
    - Sólo muestra: nombre, camas, precio, government_area
    - Ordenados por precio.

```
use('listingsAndReviews');
db.listingsAndReviews.find(
    {"address.country": "Spain"},
    {
        "_id": 0,
        "price":1, 
        "name": 1,
        "beds": 1,
        "address.government_area":1
    }
).sort({"price": -1}).limit(10);
```
#### Filtrando
- Queremos viajar cómodos, somos 4 personas y queremos:
    - 4 camas.
    - Dos cuartos de baño.
```
use('listingsAndReviews');
db.listingsAndReviews.find(
    {
        "address.country": "Spain",
        "beds" : {$eq: 4},
        "bathrooms" :{$eq: 2}
    },
    {
        "_id": 0,
        "price":1, 
        "name": 1,
        "beds": 1,
        "bathrooms":1,
        "address.government_area":1
    }
);
```
- Al requisito anterior, hay que añadir que nos gusta la tecnología queremos que el apartamento tenga wifi.
```
use('listingsAndReviews');
db.listingsAndReviews.find(
    {
        "address.country": "Spain",
        "beds" : {$eq: 4},
        "bathrooms" :{$eq: 2},
        "amenities" : {$in: ["Internet","Wifi"]}
    },
    {
        "_id": 0,
        "price":1, 
        "name": 1,
        "beds": 1,
        "bathrooms":1,
        "address.government_area":1,
    }
);


```
- Y bueno, un amigo se ha unido que trae un perro, así que a la query anterior tenemos que buscar que permitan mascota Pets Allowed
```
use('listingsAndReviews');
db.listingsAndReviews.find(
    {
        "address.country": "Spain",
        "beds" : {$eq: 4},
        "bathrooms" :{$eq: 2},
        "amenities" : {$in: ["Internet","Wifi","Pets Allowed"]}
    },
    {
        "_id": 0,
        "price":1, 
        "name": 1,
        "beds": 1,
        "bathrooms":1,
        "address.government_area":1,
    }
);

```
#### Operadores lógicos
- Estamos entre ir a Barcelona o a Portugal, los dos destinos nos valen, peeero... queremos que el precio nos salga baratito (50 $), y que tenga buen rating de reviews
```

use('listingsAndReviews');
db.listingsAndReviews.find(
    {
        $or: [
            {"address.country": "Spain"},
            {"address.country": "Portugal"},
        ],
        "price": {$lte: 50},
        "review_scores.review_scores_rating": {$gte: 70},
    }
).sort({"price": 1});
```
#### Agregaciones
- Queremos mostrar los pisos que hay en España, y los siguiente campos:
    - Nombre.
    - De que ciudad (no queremos mostrar un objeto, solo el string con la ciudad)
    - El precio
```
use('listingsAndReviews');
db.listingsAndReviews.find({"address.country": "Spain"}, {"_id":0, "name": 1, "price": 1, "address.market": 1});


use('listingsAndReviews');
db.listingsAndReviews.aggregate([
    {
        $match: {
            "address.country": "Spain"
        }
    },
    {
        $project: {
            _id: 0, 
            name: 1,
            "address.market": 1, 
            price: 1,

        }
    }
]).pretty();
```
- Queremos saber cuántos alojamientos hay disponibles por país.
```
use('listingsAndReviews');
db.listingsAndReviews.aggregate([
    {
        $group: {
            _id: "$address.country",
            count: { $sum: 1 }
        }
    },
    {
        $project: {
            _id: 0, 
            country: "$_id",
            count: 1
        }
    }
]).pretty();

```
