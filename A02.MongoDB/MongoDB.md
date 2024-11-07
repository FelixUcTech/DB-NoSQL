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

#### Comandos necesarios desde terminal

**Para levantar el servicio o la imagen de mongo**

Para este caso práctico lo estamos corriendo en Ubuntu 

```sh

docker-compose up -d mongodb

```



## Conclusiones