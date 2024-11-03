# Mongo DB
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


