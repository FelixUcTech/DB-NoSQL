# Mongo DB

## Introducción
Mongo DB representa el 45% del mercado en bases de datos a Julio del 2024, al igual que algunos servicios privados como Azure Cosmos DB, conecta con ciertas herramientas. Es un poliglota persistente.


Mongo DB guarda los datos en formato BSON (binary JSON)

Atlas se puede llegar a considerar cómo un poliglota persistente. MongoDB Atlas es una plataforma de base de datos en la nube gestionada para bases de datos MongoDB. Permite crear, desplegar y escalar bases de datos MongoDB sin preocuparse por la infraestructura, ya que gestiona automáticamente el rendimiento, la seguridad y las copias de seguridad.

Una ventaja para este tipo de tecnologías es que pueden tener una topología de nodos, funcionan como redundancias de servicio, en cuanto se pe

![Topoligia 1](/A02.MongoDB/A02.MongoDB-Imagenes/topologia.png)

![Topoligia 2](/A02.MongoDB/A02.MongoDB-Imagenes/topologia2.png)


Nuestra base de datos en la nube requiere que configuremos
- Un usuario este se configura en la nube, sin embargo que nuestra base de datos este en la nube no indica que cualquiera se pueda conectar desde cualquier lado, en la sección de NetworkAccess tenemos declaradas las ips a las que les damos permiso de hacer el login.
    - Peering podría decirse que es una forma avanzada de utilizar las configuraciones de acceso a la red que se tengan en algun servicio de nube.

### Información de apoyo

https://gist.github.com/nicobytes/fbd8c63977217855ba8afd3e240651c9

## ¿Qué son los documentos y colecciones?
### Documentos
Un documento es una forma de organizar y almacenar información con un conjunto de pares **clave-valor** o **campo-valor**, este conjunto de información es alusivo a un dominio, es decir, la información de un producto, curso, campaña, inventario, etc, ese sería el dominio o igual llamadas identidades.

Ejemplo 1, se muestra un documento clave-valor, este está conformado de 2 relaciones de clave-string, 1 de clave-entero, y otro de clave-vector de estring, entonces ese es un ejemplo de documento.

``` json
{
    name: "Félix",
    age: 26,
    status: "A",
    groups: ["Guitarra clasica", "Ballet Clasico", "Idiomas", "Ingeniería", "Filosofía", "Espiritualidad"]
}
```

Este es un documento donde se anexan un clave-documento, esto le podemos llamar subdocumentos, por ejemplo loc (location)

``` json
{
    "_id": "djfhadsf435435q",
    "city": "Villahermosa",
    "zip": "86288",
    "loc": {
        "y": "33.331165",
        "x": "86.208934"
    },
    "pop": 3062,
    "state": "ALL"
}
```
Por ejemplo si queremos guardar los contacto en una aplicación, tendríamos que crear un modelo de datos, con el nombre de la persona, y la información que quieras de la persona. La belleza de la bases NoSQL se aprecian si al guardar un nuevo contacto, el campo del segundo es mas extenso cómo las redes sociales y otros datos, a diferencia del primero, en la tabla que usaríamos en SQL tendriamos mucho espacio de memoria sin usar, lo cual en un NoSQL no sucede, porque solamente se anexa un campo en el segundo contacto donde esta esa información extra. **Es decir el esquema es flexible**

**Primer contacto**
```json
{
    "name": "Félix",
    "age": 26,
    "status": "A",
    "groups": ["Guitarra clasica", "Ballet Clasico", "Idiomas", "Ingeniería", "Filosofía", "Espiritualidad"]
}
```

**Segundo contacto**
```json
{
    "name": "María",
    "age": 30,
    "status": "A",
    "groups": ["Literatura", "Arte", "Idiomas"],
    "social_media": {
        "facebook": "maria123",
        "twitter": "@maria",
        "linkedin": "maria-linkedin"
    },
    "additional_info": {
        "favorite_books": ["Cien Años de Soledad", "El Principito"],
        "hobbies": ["Pintura", "Senderismo"]
    }
}
```

### Colecciones

MongoDB almacena documentos en una colección, usualmente con campos comunes entre sí, es decir que cada documento que pertenece a una colección puede tener campos clave-valor en común o esa sería la idea, la flexibilidad no está en que todo sea diferente más bien pueda agregarse información si así se requiere, sea o no de caracter obligatorio de acuerdo al esquema de datos.

![Coleccionnes-Documentos](/A02.MongoDB/A02.MongoDB-Imagenes/Colecciones-Documentos.png)


## Atlas la versión en la Nube de MongoDB
Una herramienta gratuita para empezar aprender acerca de las bases NoSQL, es atlas una versión que puede ser gratuita y te ayuda a concoer el manejo de una base de datos NoSQL.

https://www.mongodb.com/es/cloud/atlas/register

https://www.mongodb.com/es


## Trabajando MongoDB en Visual Studio Code 

### Extesión MongoDB-for-VS-Code

Para realizar esta configuración es necesario intalar la estisión:

![MongoDB-for-VS-Code](/A02.MongoDB/A02.MongoDB-Imagenes/MongoDB-for-VS-Code.png)

Esta herramienta nos permite guardar nuestras consultar y dar una facilidad de trabajo en un entorno que puede ser más familiar, ejemplo el editor de codigo Visual Studio Code.

**Para conectarse a la base de datos en la nube**

La base de datos se configura en la nube, Atlabas de MongoDB, posterioremente en MongoCompass Se conecta uno a la base de datos, ahí el string o uri, se usa para que mediente la estesión de Mongo de BD se pueda conectar uno a la base de datos.

![coneccion-mongodb-vsc](/A02.MongoDB/A02.MongoDB-Imagenes/coneccion-mongodb-vsc.png)

### Docker
Es necesario instalar dooker, dado que los dirver requeridos para correr alguna tecnología pueden ser gestionados por Docker y es cómo crear un entorno virtual donde se integra todo lo necesario para trabajar diferentes bases de datos para este caso práctico.

https://docs.docker.com/desktop/install/windows-install/

Dentro de nuestro prograna hay un archivo .yml el cual sirve para: Los archivos .yml (YAML) no están sujetos a versiones específicas, **ya que YAML es un lenguaje de serialización de datos más que un software en sí. Sin embargo, YAML ha pasado por varias versiones de especificación, siendo la versión 1.2, publicada en 2009, la más reciente y ampliamente usada en la actualidad.** Esta versión incluye mejoras de compatibilidad con JSON, lo que facilita la interoperabilidad entre ambos formatos.

**En herramientas o entornos específicos que usan archivos .yml, como Docker, Kubernetes o GitHub Actions, cada uno puede interpretar YAML según sus propias necesidades y reglas, pero todos basados en la especificación general de YAML 1.2.**

Cuando en un archivo .yml ves una línea como version: "3.9", esto generalmente no hace referencia a la versión de YAML, sino a la versión de la especificación del servicio o herramienta que usa YAML como formato de configuración. Por ejemplo:

- En Docker Compose, version: "3.9" indica la versión de la especificación de Docker Compose que estás utilizando. Docker Compose tiene varias versiones (como 2, 3, 3.7, 3.8, 3.9), y cada una agrega soporte para diferentes características. Actualmente, 3.9 es una de las versiones más recientes y es compatible con Docker Engine 20.10 en adelante. **(05-11-2024)**

- En Kubernetes o en otras herramientas de orquestación, puede aparecer una versión para especificar compatibilidad con ciertos recursos o configuraciones en YAML, pero su manejo es diferente al de Docker Compose.

La versión que deberías usar depende de la versión de Docker o la herramienta que tengas instalada y de las funcionalidades que necesites. Para la mayoría de los proyectos actuales en Docker Compose, 3.9 es una buena opción por su compatibilidad y soporte de características actualizadas.

[Doker Compose .YML](/A02.MongoDB/docker-compose.yml)

#### Volumenes
En Docker, los volúmenes son mecanismos de almacenamiento que permiten mantener datos de forma persistente incluso cuando el contenedor se detiene o se elimina. Esto es útil en aplicaciones que necesitan almacenar datos que sobreviven a los ciclos de vida de los contenedores, como bases de datos y archivos de configuración.

En un archivo docker-compose.yml, los volúmenes se pueden definir para mapear un directorio de tu host (tu sistema) a un directorio dentro del contenedor, o para crear un volumen gestionado por Docker que almacene los datos en una ubicación independiente. Esto facilita la administración de datos en aplicaciones que requieren persistencia.

##### ¿Cómo funcionan los volúmenes en Docker Compose?

- Declaración de volúmenes en el servicio: En cada servicio, puedes definir uno o más volúmenes que quieras montar.
    
- Definición de volúmenes globales: Docker Compose también permite declarar volúmenes a nivel global para que se puedan compartir entre diferentes servicios si es necesario.

``` yaml
version: '3.9'

services:
  mongoDB:
    image: mongo:8.0
    ports:
      - 27017:27017
    environment:
      - MONGO_INITDB_ROOT_USERNAME=USER
      - MONGO_INITDB_ROOT_PASSWORD=Password01
    volumes:
      - mongo_data:/data/db  # Monta el volumen "mongo_data" en /data/db dentro del contenedor
```
Esto significa que todo lo que se guarde en el directorio /data/db dentro del contenedor se almacenará en el volumen mongo_data, permitiendo que los datos persistan aunque el contenedor se elimine.
volumes a nivel global:

 - La sección volumes: en la parte inferior del archivo declara mongo_data como un volumen gestionado por Docker. Si el volumen aún no existe, Docker lo creará automáticamente.

Una vez creada la intancia en Docker se puede acceder mediante:


![coneccion-mongodb-vsc](/A02.MongoDB/A02.MongoDB-Imagenes/coneccion-mongodb-vsc.png)


#### Comandos necesarios desde terminal

**Para levantar el servicio o la imagen de mongo**

Para este caso práctico lo estamos corriendo en Ubuntu 

```sh
docker-compose up -d mongoDB
```

**Comando para hacer el check (verificar el servicio)**

```sh
docker-compose ps
```

### Conexión mediante terminal de mongo

Hacia Atlas, la versión en la nube o hacia Docker la versión local de la base de datos.

Vamos a correr la terminal de mongo desde el contenedor, dado que el contenedor que generamos ya tiene corriendo la terminal mencionada.

Para conectar con el servicio que está corriendo en Docker, la terminal de mongo usamos el siguiente comando

**Ejecutamos en Ubuntu**

``` sh
docker-compose exec mongoDB bash
```
Algunos parametros pueden no ser necesarios para levantar el servicio de la terminal

```
felixuctech@FELIXPOWER:~/DB-NoSQL/A02.MongoDB$ docker-compose exec mongoDB bash
WARN[0000] /home/felixuctech/DB-NoSQL/A02.MongoDB/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
```
Normalmente los contenedores están basados en sistemas IUNIX, por lo cual puedes encontrar ese mismo sistema de carpetas. Y los comando son similares a la distribución que manejes.

![terminalmongo](/A02.MongoDB/A02.MongoDB-Imagenes/terminalmongo.png)

Desde la terminal **mongosh** Nos podemos conectar a cualquier Instacia de mongo, la del Docker que es local o la de la nube que es de prueba de Atlas.

**Para conectarnos con Mongo Hacia Docker**
``` sh
mongosh "mongodb://localhost:27017/?tls=false"
```

Para salir de La instancia de mongo se require escribir "exit" y enter, ahora si se puede acceder a la siguiente:

**Para conectarnos con Mongo Hacia Atlas**
``` sh
mongosh "mongodb+srv://demotest:1998felix123456@demoatlasfelix.xds4g.mongodb.net/"
```
En cada instancia se puede correr los compando de prueba en que se encuentra en: 

[QueryHaciaAtlas.mongodb](/A02.MongoDB/src/QueryHaciaAtlas.mongodb)

[QueryHaciaDocker.mongodb](/A02.MongoDB/src/QueryHaciaDocker.mongodb)




## JSON VS BSON

Normalmente Mongo guarda en BSON pero nosotros interactuamos con el formato de JSON por facilidad de lectura y entendiemiento. Esto por rendimiento.

### JSON

Ventajas:

- Amigable
- Se puede leer
    - Es un formato clave valor
- Es un formato muy usado

Desventajas: 

- Está basado en texto, por ende consume mucho espacio
- Es limitado, solo puede guardar:
    - String
    - Boolean
    - Number
    - Arrays

### BSON
Es una creación de Mongo, es un formato más optimizado pero muy dificil de leer:

![JsonVSBson](/A02.MongoDB/A02.MongoDB-Imagenes/jsonVSbson.png)

Ventajas:

- Representación binaria de JSON
- No consume espacio
- Alto rendimiento
- Tipos de datos: +, date, raw binary, integer, long,  float

Desventajas: 

- No es estandar
- Es un leguaje para la maquina 

Más acerca de BSON

https://www.mongodb.com/resources/languages/bson


## Comandos interesantes

updateOne afecta solo un documento, el primero que coincida con el filtro.
updateMany afecta todos los documentos que coincidan con el filtro.

### Operadoor pop

Remueve el primer o ùltimo elemento de un array

```javascript
db.students.updateOne(
   { _id: 1 },
   { $pop: { grades: -1 } }
)
```
### Eliminar documentos

```javascript
use("Tienda")
db.Productos.delaateOne({_id: 1})
//or
db.Productos.delaateOne({_id: ObjectId("stringgenerado")})
```

## Operadores

### $eq y $ne
Uso de DataSet

``` js
use("platzi_store")

db.inventory.drop()

db.inventory.insertMany([
  { _id: 1, item: { name: "ab", code: "123" }, qty: 15, tags: ["A", "B", "C"] },
  { _id: 2, item: { name: "cd", code: "123" }, qty: 20, tags: ["B"] },
  { _id: 3, item: { name: "ij", code: "456" }, qty: 25, tags: ["A", "B"] },
  { _id: 4, item: { name: "xy", code: "456" }, qty: 30, tags: ["B", "A"] },
  { _id: 5, item: { name: "mn", code: "000" }, qty: 20, tags: [["A", "B"], "C"] },
])

db.inventory.find()
```
Es un operador que viene de manera inplicita en algunos comandos que regularmente se usan en consutas en MongoDB.

En el DataSet Anterior si usamos una consulta como la siguiente implicitamente ya estamos usando un equal:

``` js
use("platzi_store")

//Using $eq
db.inventory.find({qty: 20})
```

El ejemplo anterior de manera explicita es el siguiente:

``` js
use("platzi_store")

//Using $eq
db.inventory.find({qty: {$eq: 20}})
```
Consulta a un subdocumento
``` js
use("platzi_store")

//Using $eq
db.inventory.find({ "item.code": "123"})
```
El ejemplo siguiente es para cuando queremos hacer una actualización de varios campos basados en una condición de exclusión mediante el $ne, **Todo lo que no sea igual a 20 debería incrementar**
``` js
use("platzi_store")
//Using $ne
db.inventory.updateMany(
    { "qty": {$ne: 20}}, //Condición de busqueda
    {$inc: {qty: 10}} //Actualización
)
```






### $gt, $gte, $lt y $lte
Estos comparadores nos permiten hacer comparaciones entre números.

```js
// Using $gt (>: Mayor que) $gte (>= Mayor Igual)

// Traera los valores 25 y 30, pero no partira por el 20, asi cumple Mayor que 20
use("platzi_store")
db.inventory.find({ qty: { $gt: 20 } })

// Traera los valores 20, 25 y 30, sacara solamente el n°15 y asi cumple Mayor Igual que 20
use("platzi_store")
db.inventory.find({ qty: { $gte: 20 } })

// Using $lt (<: Menor que) $lte (<= Menor Igual)

// Traera solo el valor 15 y excluiria todos los otros valores
use("platzi_store")
db.inventory.find({ qty: { $lt: 20 } })

// Traera solo los valores 15 y 20 del documento y excluiria todos los otros valores
use("platzi_store")
db.inventory.find({ qty: { $lte: 20 } })

// Join - Traer todo los archivos que sean Mayor igual 25 y Menor igual 35 = El resultado debe ser 25 y 30
use("platzi_store")
db.inventory.find({ qty: { $gte: 25, $lte: 35 } })

// Join - Traer todo los archivos que sean Mayor igual 20 y Menor igual 35 = El resultado debe ser 20 y 25
use("platzi_store")
db.inventory.find({ qty: { $gte: 20, $lte: 25 } })

// New join

// Segun la condicional no hay ningun sub documento que tenga qty:20 y 25
use("platzi_store")
db.inventory.find({
    "item.name": "ab",
    qty: { $gte: 20, $lte: 25 }
})

// Segun la condicional solo aparecera un dato, porque solamente se encuentra en el code 123
use("platzi_store")
db.inventory.find({
    "item.code": "123",
    qty: { $gte: 20, $lte: 25 }
})

// Quiero que no busque mi sub documento 123 y si los demas que tengan qty Mayor Igual 20 y Menor Igual 25
use("platzi_store")
db.inventory.find({
    "item.code": { $ne: "123" },
    qty: { $gte: 20, $lte: 25 }
})
```

### $regex
Este comando se utiliza como para realizar filtrol en base a texto, se apoya de expresiones regulares para poder hacer los filtro.

Para este ejemplo se usa el siguiente DataSet

``` js
use("platzi_store")

db.inventory.drop()

db.inventory.insertMany([
  { _id: 1, item: { name: "ab", code: "123", description : "Single line description."    }, qty: 15, tags: ["A", "B", "C"] },
  { _id: 2, item: { name: "cd", code: "123", description : "First line\nSecond line"     }, qty: 20, tags: ["B"] },
  { _id: 3, item: { name: "ij", code: "456", description : "Many spaces before     line" }, qty: 25, tags: ["A", "B"] },
  { _id: 4, item: { name: "xy", code: "456", description : "Multiple\nline description"  }, qty: 30, tags: ["B", "A"] },
  { _id: 5, item: { name: "mn", code: "000" }, qty: 20, tags: [["A", "B"], "C"] },
])

db.inventory.find()
```
Consultas con expresiones regulares.

```js
use("platzi_store")

// Filtro dentro de un subdocumento
// En este caso tiene que coicidir identicamente
db.inventory.find({"item.description": "Single line description."})

// Para una busca más general usamos expresiones regulares
// $regex: /????/ busca de manera general en description si existe line
// Esta forma es sensible a las mayúsculas y minúsculas
db.inventory.find({"item.description": {$regex: /line/ }})

// $regex: /????/ busca de manera general en description si existe line
// Esta forma no es sensible a las mayúsculas y minúsculas
db.inventory.find({"item.description": {$regex: /lInE/i }})

//Busca algo que termine en una palabra o terminación específica
db.inventory.find({"item.description": {$regex: /lInE$/i }})

//Busca algo que inicie en una palabra o prefijo específica
db.inventory.find({"item.description": {$regex: /^single/i }})

```

### Projection

Cuando requieres operar distintos valores, es importante entender que no siempre es bueno tener toda la información para ello la herramienta moestrda desde el entornode MongoCompass **nos permite seleccionar únicamente lo que requerimos para una manipulación y uso de datos más específica mediante 1 o 0.**

![Projection](/A02.MongoDB/A02.MongoDB-Imagenes/Project.png)

Utilizando nuestra base de datos de MongoCompass

```js

//Projection
use("sample_training")
db.trips.find(
    //Consulta similar al where en sql
    {tripduration: {$gte: 500}},
    //projection (Apagar o encender información de la consultar)
    {tripduration: 1, usertype: 1}
)

//Projection
use("sample_training")
db.trips.find(
    //Consulta similar al where en sql
    {tripduration: {$gte: 500}},
    //projection (Apagar o encender información de la consultar)
    //Desactivar explicitamente el _Id
    {tripduration: 1, usertype: 1, _id: 0}
)

```

### Operadores para Arrays

#### DataSet
Para realizar ejercicios con arrays carguemos a nuestras bases de datos la siguiente información:

``` js
use("platzi_store")

db.inventory.drop()

db.inventory.insertMany([
  { _id: 1, item: { name: "ab", code: "123", description : "Single line description."    }, qty: 15, tags: [ "school", "book", "bag", "headphone", "appliance" ] },
  { _id: 2, item: { name: "cd", code: "123", description : "First line\nSecond line"     }, qty: 20, tags: [ "appliance", "school", "book" ] },
  { _id: 3, item: { name: "ij", code: "456", description : "Many spaces before     line" }, qty: 25, tags: [ "school", "book" ] },
  { _id: 4, item: { name: "xy", code: "456", description : "Multiple\nline description"  }, qty: 30, tags: [ "electronics", "school" ] },
  { _id: 5, item: { name: "mn", code: "000" }, qty: 20, tags: [ "appliance", "school" ] },
])

db.survey.drop();

db.survey.insertMany([
  {
    _id: 1,
    results: [
      { product: "abc", score: 10 },
      { product: "xyz", score: 5 },
    ],
  },
  {
    _id: 2,
    results: [
      { product: "abc", score: 8 },
      { product: "xyz", score: 7 },
    ],
  },
  {
    _id: 3,
    results: [
      { product: "abc", score: 7 },
      { product: "xyz", score: 8 },
    ],
  },
  {
    _id: 4,
    results: [
      { product: "abc", score: 7 },
      { product: "def", score: 8 },
    ],
  },
]);
```

#### $in & $nin (OR)

El **$in** Funciona para entrar en los arrays y solicitar la información a manera de **OR lógica**, sin embargo, el **$nin** es lo contrario es una exclusión de los campos mencionados en la consuta, lo más peculiar de estos dos comandos es que son usados par avalores y para arrayas

```js
use("platzi_store")
// in, valores y arrays
db.inventory.find(
    { qty: {$in: [20, 25] } }//,
    //{qty: 1, }
)

use("platzi_store")
// in, valores y arrays
db.inventory.find(
    { tags: { $in: ["book", "electronics"] } }
)

use("platzi_store")
// nin, valores y arrays
db.inventory.find(
    { qty: {$nn: [20, 25] } }//,
    //{qty: 1, }
)

use("platzi_store")
// nin, valores y arrays
db.inventory.find(
    { tags: { $nin: ["book", "electronics"] } }
)
```

#### Consulta a arrays sin operador

Cuando hacemos una consulta directamente a array, sin ningún operador, indicará que a la clave que estemos apuntando solo nos arrojará un resultado la consulta si cumple estrictametente con el valor de la consulta.

```js
use("platzi_store")
//Manda a llamar los que tenga los elementos
db.inventory.find({tags: "book"})
//Cumple y manda a llamar el documento correcpondiente
db.inventory.find({tags: ["school","book"]})
//No hay en el dataset un documento que cumpla estrictamente
db.inventory.find({tags: ["book","school"]})
```

#### all  (AND)
El operador all dentro de arrays ya es un homologo de una compuerta lógica and, este nos permite consultar dentro de los arrays los elementos que tengan n canctidad de elementos en forma condicionadal para que realice la consuta.

```js
use("platzi_store")
// all, valores y arrays
db.inventory.find(
    { qty: {$all: [20, 25] } }//,
    //{qty: 1, }
)

use("platzi_store")
// all, valores y arrays
db.inventory.find(
    { tags: { $all: ["book", "electronics"] } }
)
```

#### $size
Retorna los documentos en función de un número arbitrio de elementos que contega el array "[]", es decir si yo le indico que cuente los elementos de un arreglo y tenga 2 elemento, me retornara los documentos donde haya sólo dos elementos.

```js
use("platzi_store")
db.inventory.find(
    { tags: {$size: 2 } } //Query
)
```

#### $elemMatch
Este nos sirve cuando tenemos un array con objetos dentro, y nor sirve para ser más específicos en el query. ejemplo se muestra en la colección de **survey** [DataSet](#dataset).

```js
//Uso del elemMatch
use("platzi_store")
db.survey.find({
    results: {
        $elemMatch: {
            product: "abc",
            score: {$lte: 7}
        }

    }
})
```

### Operadores Lógicos
#### AND

![AND](/A02.MongoDB/A02.MongoDB-Imagenes/and.png)

Se puede llegar a creer que la condicional and se trabaja normalmente de manera implicita, pero la forma explicita es más clara

¿Qué consume menos recurso,l la forma implicita o la forma explicita?

```js
//AND implicito
use("sample_training")
db.inspections.find({
    sector: {$regex: /TAX PREPARERS - 89/i},
    result: {$regex: /Unable to/i}
})

//AND explicito
// and: [{},{},{},{}]
use("sample_training")
db.inspections.find({
    $and: [
        {sector: {$regex: /TAX PREPARERS - 89/i}},
        {result: {$regex: /Unable to/i}}
    ]
})
```

#### OR

![OR](/A02.MongoDB/A02.MongoDB-Imagenes/or.png)

Aquí es importante hacer la consulta para multiples atributos

```js
//OR explicito
// OR: [{},{},{},{}]
use("sample_training")
db.inspections.find({
    $or: [
        {sector: {$regex: /TAX PREPARERS - 89/i}},
        {result: {$regex: /Unable to/i}}
    ]
})
```
#### NOR

![NOR](/A02.MongoDB/A02.MongoDB-Imagenes/nor.png)

Es todo lo que no incluye mi cunsulta

```js
//NOR explicito
// NOR: [{},{},{},{}]
use("sample_training")
db.inspections.find({
    $nor: [
        {sector: {$regex: /TAX PREPARERS - 89/i}},
        {result: {$regex: /Unable to/i}}
    ]
})

//Adición de una proejction
//NOR explicito
// NOR: [{},{},{},{}]
use("sample_training")
db.inspections.find(
  {
    $nor: [
        {sector: {$regex: /TAX PREPARERS - 89/i}},
        {result: {$regex: /Unable to/i}}
    ]
  },
  //projection
  {
    result: 1,
    _id:  0
  }
)
```
#### not
Este no trabaja con array, se usa directamente en un atributo.

```js
//not
// not: {}
use("sample_training")
db.inspections.find({
    result: {$not: {$regex: /TAX PREPARERS - 89/i}}
},
//Projection
{
    result: 1
}
)
```

#### Funciones logicas combinadas
Se pueden usar las condiciones lógicas de manera combinada

Para saber un vuelo en específico que salga o aterrice en el aeropuesto de bogota

```js
//Vuelo X llegada o salida desde el mismo aeropuerto
use(sample_training)
db.routes.find({
    $and: [
        //Definir el vuelo
        {
            airplane: "E70"
        },
        //Definir or, ya sea que llegue o salga
        {
            $or: [
                {dst_airport: "BOG"},
                {src_airport: "BOG"}
            ]
        }

    ]
})
```





### Expresive operator

#### DataSet

```js
use("platzi_store")

db.monthlyBudget.drop()

db.monthlyBudget.insertMany([
  { "_id" : 1, "category" : "food",    "budget": 400, "spent": 450 },
  { "_id" : 2, "category" : "drinks",  "budget": 100, "spent": 150 },
  { "_id" : 3, "category" : "clothes", "budget": 100, "spent": 50  },
  { "_id" : 4, "category" : "misc",    "budget": 500, "spent": 300 },
  { "_id" : 5, "category" : "travel",  "budget": 200, "spent": 650 }
])

db.monthlyBudget.find()
```
#### Consulta utilizando operadores en forma expresiva 
En MongoDB, los operadores de expresión permiten realizar comparaciones y manipulaciones de valores dentro de una consulta. Estos operadores se pueden usar en el campo de consulta para realizar cálculos y evaluaciones dinámicas en cada documento que se procesa. Entre ellos, el operador `$expr` es especialmente útil para realizar comparaciones y operaciones que involucren múltiples campos del mismo documento.


El operador `$expr` permite utilizar expresiones de agregación dentro de una consulta. Esto permite comparar valores de diferentes campos del mismo documento o realizar cálculos más avanzados dentro de las condiciones de consulta. `$expr` es muy útil cuando necesitas hacer una comparación que involucre más de un campo en el mismo documento, ya que no es posible hacerlo directamente con otros operadores de comparación como `$gt`, `$lt`, `$eq`, etc.


```js
use("sample_training")
db.trips.find({
    $and: [
        {$expr: {
            $eq: ["$start station id", "$end station id"]
        }},
        {tripduration: {
            $gte: 1200
        }}
    ] 
}).count()
```


## Herramientas comunes al trabajar con MongoDB

### Aggregation Framework
Es una herramienta pensada para tener analisis de datos casi a nivel de datascienc. Pero el agregation framework es mucho más robusto dado que se pueden generar piplines [¿Qué es un data pipline?](/A01.Fundamentos/Fundamentos.md), es más para procesar datos. Sobre todo cuando hablamos en grandes volumenes de datos.

![Aggregation Framework](/A02.MongoDB/A02.MongoDB-Imagenes/Aggregation%20Framework.png)

Ya no usamos la función find, sino la función aggregate, pero lo mas poderoso de esta forma de operar es que funciona por capas. 

```js
//Aggregation Framework
use("sample_airbnb")
db.listingsAndReviews.aggregate([
    {$match: {
      amenities: "Wifi"
    }}, //find
    {$project: {
      address: 1
    }}, //projection
    {$group: {_id: "$address.country", count: {$sum: 1} } } //Agrupar
])
```
### Sort, limit & skip 
```js
//sort, limit y skip 
use("sample_training")
db.zips
    .find({
        pop: { $lte: 99 }
    })
    //sort:1 - Ascendente (números y letras)
    .sort({
        pop: -1
    })
    .limit(2)
```
Para técnicas de paginación:

```js

```


## Conclusiones

