# Practicas MongoDB
## _La muerte a collejas por lemonCode_

Para esta practica se nos solicita:

- Montar un contendor docker de mongo y restaurar una base de datos de airbnb
- Saca en una consulta cuántos apartamentos hay en España.
- Lista los 10 primeros 

## Requerimientos previos
Creamos una imagen de docker y con run de paso el contenedor
$docker run --name my-mongo-db-airbnb -p 27017:27017 -d mongo:4.4.3
Si no estuviese el contenedor corriendo ejecutamos un start
$docker start my-mongo-db-airbnb
Copiamos la base de datos que nos hemos descargado al contenedor
$docker cp backup my-mongo-db-airbnb:/opt/app
Nos conectamos en modo interactivo, chequemos que están las bases de datos 
$docker exec -it my-mongo-db-airbnb
#cd ./opt/app
#ls
listingsAndReviews.bson  listingsAndReviews.metadata.json
sin salir del modo interactivo restauramos la base de datos de airbnb
#mongorestore --db listingsAndReviews .

## Parte Obligatoria

Generar un modelado que refleje los siguiente requerimientos:

>Queremos mostrar los últimos cursos publicados. Para ello se añade el campo "fecha de publicación" en el documento "videos"

>Queremos mostrar cursos por área (devops / front End ...). Para ello, en el documento "videos" hay un objeto llamado "tematica", a traves del cual se pone a true cada tematica que trata el video, pudiendo tratar no solo una tematica, ya que a veces se enrtelazan las tematicas de los cursos.

>Queremos mostrar un curso con sus videos. Para ello tenemos el documento "curos" un campo con un array sobre los cursos que pertenece

>En un video queremos mostrar su autor. Para ello tenemos el docuento "videos" relacionado con el documento "autores"

## Documentos
- Modelado_documental.dmm, donde tratamos de mostrar una ejemplificación de modelado documental 
    - ✅ Modelado No SQL.
    - ❌ Modelado SQL

- Modelado_relacional.dmm, donde tratamos de mostrar una ejemplificación de modelado documental.
    - ❌ Modelado No SQL.
    - ✅ Modelado SQL
## Patrones de diseño

Para esta practica hemos intentado aplicar dos patrones de diseño.

| Patron | Descripcion | Ejemplo |
| ------ | ------ | ------ |
| Subset Pattern | Nos permite manejar documentos grandes |Tengo un documento de reseñas, pero he creado un reseñas_last_3 en el docuemento videos (tendra las 3 ultimas reseña) para evitar tirar de un documento reseñas grande.  |
| Extended ref | Evita tener que hacer "joins" |En el documento suscriptores he embebido la direccion para tener que tirar de doble consulta a este dato cuando adquiramos del cliente. Se ha hecho un objeto que bien podria ser un array de objetos si quisieramos añadir varias direcciones. |
