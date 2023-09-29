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

- Saca en una consulta cuántos apartamentos hay en España.


use('listingsAndReviews');

db.listingsAndReviews.count({"address.country": "Spain"});

resultado:

633

- Lista los 10 primeros
