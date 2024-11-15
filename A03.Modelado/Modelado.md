# Modelado de Datos en MongoDB

## Metodología para el modelado de Datos

### Limitación por el Hardware
Para entender este concepto es importante especificar nuestro contexto, si el desarrollo de la base de datos cuenta con "n" cantidad maquinas virutales, si el escalamiento de fue vertical u horizontal, en si donde será hospedada toda la configuración y datos de la misma, considerar esto nos ayuda a entender el paradigma de los recursos que se tienen limitados y los que no (RAM, CP, MEMORIA(Donde está almacenado)).

### Limitación por software
**Los documentos devueltos por la consulta de agregación, ya sea como cursor o almacenados mediante $out en otra colección, están limitados a 16MB. Es decir, no pueden ser mayores que el tamaño máximo de un documento de MongoDB.** [Fuente](https://studio3t.com/es/knowledge-base/articles/mongodb-aggregation-framework/#:~:text=Los%20documentos%20devueltos%20por%20la%20consulta%20de%20agregaci%C3%B3n%2C,el%20tama%C3%B1o%20m%C3%A1ximo%20de%20un%20documento%20de%20MongoDB.)

Existen buenas prácticar para que un documento no sea tan pesado, desde el modelado de la base de datos. 

### Limitación por latencia

¿Dónde está ubicado tu usuario y cuanto le cuesta llegar hasta el servidor que le va proveer la información?

Una Content Delivery Network (CDN) es una red de servidores distribuidos geográficamente que trabajan en conjunto para entregar contenido de manera rápida y eficiente a usuarios de internet. Al almacenar copias en caché del contenido (como archivos multimedia, páginas web y otros recursos) en múltiples ubicaciones, una CDN reduce la distancia entre el usuario y el servidor que tiene el contenido, mejorando la velocidad de carga y la experiencia del usuario.

- Beneficios de usar una CDN:
    - Los servidores distribuidos entregan el contenido desde el nodo más cercano al usuario.
    - Si un servidor falla, otro en la red puede asumir la entrega del contenido.
    - Soporta un alto tráfico sin saturar el servidor principal.
    - Al utilizar el almacenamiento en caché, la CDN disminuye la cantidad de datos que el servidor principal necesita enviar.




![Latencia](/A03.Modelado/A03.Modelado-Imagenes/Latencia.png)

### Metodología
1. Requerimientos
2. Identificar Entidad-Relación
3. Aplicar patrones

Cada una de estas fases implica evaluar en cuatro parámetros. 

1. Escenarios

2. Expertos

    En la unidad de negocio.

3. Sistema Actual

    Como maneja la compañia su información

4. DB Admin
    Nosotros como expertos en modelado.

![Metodología](/A03.Modelado/A03.Modelado-Imagenes/Metodología.png)

Parte de ese diseño:

- Workload: Tamaño de los datos, consulta e indices, operaciones importantes, supocisiones.
- Relaciones: Identificar entidades, identificar atributos, identificar relaciones, embeber vs referenciar.
- Patrones:  Identificar y aplicar.

## Crar una Base de Datos
![DB](/A03.Modelado/A03.Modelado-Imagenes/DB.png)

Creación demo en MongoAtlas, el entorno virtual de MongoDB

## Wordload
**Uses Cases** 
- An organization has deployed 100M weather sensors. 
- The goal is to gather the data transmitted from all devices in one database to do predictions, and trend analysis. 

**Main Data** 
- Number of devices (100000000) 
- Analysts (10) 
- Period (10 years) 

**Assumptions** 
- Data per hour is as good as per minute for trends 
- Still need to keep the data per minute for deeper analysis 

**Operations**

    - Actor: 	sensor device
        - Description: 	send weather data to the server
        - Operation Type: 	write
        - Data in Operation: 	device ID, time stamp, device metrics
        - Frequency: 	send metric data every minute
        - Rate: 	100000000 / 1 min
        - Op Type: 	Write


    - Actor: 	Data Scientist
        - Description: 	run analytic/trend query on temperature
        - Operation Type: 	read
        - Data in Operation: 	temperature metrics
        - Frequency: 	run ~10 analytic queries per hour
        - Rate: 	10 (Data Scientist) * 10 (queries) 100 / 1h
        - Op Type: 	Read


**Entities**
- Users 
- Profile 
- Devices 
- Categories 
- Data Devices 
- airTemperature 
- Pressure 
- Wind (direction, speed) 

**Observations** 
- There is more write operators that read operations

![Workload](/A03.Modelado/A03.Modelado-Imagenes/Workload.png)

Parte de entender el worklaod, son los ajentes que están inmersos en la rubro, y entender el manejo de la data, esto para reconocer cual será las **consultas más importantes, que se podrían cargar a memoría volatil, la ram, que en mongo sería la indexación.**

En el siguiente ejemplo de entiedad relación muestra los alementos como se relacionaran en el workload.

![Identidad-Relación](/A03.Modelado/A03.Modelado-Imagenes/Entidad-Relacion.png)


## Validando Colección
[Información que cura](https://www.mongodb.com/docs/manual/reference/bson-types/)
### Strings
Qué mongoDB sea una base de datos no relacional, qué se flexible y versatil, esto no quiere decir que no respete las reglas de negocio o qué no sé estructure la información de una manera funcional. 

Una característica de mongo es definir la estructura, para validar los datos ingresado, ejemplo, si ingresamos un dato en una base de datos de usurios, y el email es un dato númerico y no tiene contraseña, ya estaríamos incurriendo en un error. 

```js
use("BaseDeDatosEjemplo")
db.usurios.insertOne({ //Colección
    last_name: "Perenganito",
    email: 123456789
}) 
```
Dentro de la estructura del documento podemos defirnir si es obligatorio o no, qué tipo de datos debe ser cada dato, etc etc.

En el siguiente esquema de validación se definen ciertos alcances, sin embargo qué el correo tennga arroba, o el tipo de Password que se tenga que poner, eso puede incluirse en otra capa.

```js
//Esto facilmente se ejecuta directamente a un entorno virual, MongoAtlas o trabajando en una base de datos local. 
use("platzi_store")//Si no existe en el cluster crea la base de datos
db.createCollection("users", { //Colection
    validator: {  //Permite definir reglas para los documentos cargados a la colección mencionada.
        $jsonSchema: { //Se define el tipo de estructura
            bsonType: "object",//Define que el tipo de datos que se espera en cada documento de esta colección sea un objeto.
            required: ["email","password"],//Se definen qué datos son obligatorio
            properties: {
                name: {
                    bsonType: "string"
                },
                last_name: {
                    bsonType: "string"
                },
                email: {
                    bsonType: "string"
                },
                password: {
                    bsonType: "string"
                }
            }
        }
    }
})
```
### Number and others
Para numbers podemos agregar un minimo y un máximo.

```js
use("platzi_store")
db.createCollection("users", { //Colection
    validator: {  //Permite definir reglas para los documentos cargados a la colección mencionada.
        $jsonSchema: { //Se define el tipo de estructura
            bsonType: "object",//Define que el tipo de datos que se espera en cada documento de esta colección sea un objeto.
            required: ["email","password", "role"],//Se definen qué datos son obligatorio
            properties: {
                name: {
                    bsonType: "string"
                },
                last_name: {
                    bsonType: "string"
                },
                email: {
                    bsonType: "string"
                },
                password: {
                    bsonType: "string"
                },
                age: { // Uso de limites en la edad
                    bsonType: "number",
                    minimum: 18,
                    maximum: 99
                },
                isSingle: {
                    bsonType: "bool"
                },
                role: {//Lista de strings acotada
                    enum: ["customer", "seller", "admin"]
                }
            }
        }
    }
})
```
**Insertar datos**
```js

```



**Ejemplo más detallado**

Nota:
- El campo pattern en el esquema JSON de MongoDB utiliza una expresión regular (regex) para definir el formato específico que debe cumplir el valor del campo.

```js
//Las descripciones solo son de apoyo
db.createCollection("usuarios", {
    validator: {
       $jsonSchema: {
          bsonType: "object",
          required: ["nombre", "edad", "email", "perfil"],
          properties: {
             nombre: {
                bsonType: "string",
                description: "Debe ser un string y es obligatorio"
             },
             edad: {
                bsonType: "int",
                minimum: 18,
                description: "Debe ser un número entero mayor o igual a 18 y es obligatorio"
             },
             email: {
                bsonType: "string",
                pattern: "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$",
                description: "Debe ser un correo electrónico válido y es obligatorio"
             },
             perfil: {
                bsonType: "object",
                required: ["direccion", "telefono"],
                properties: {
                   direccion: {
                      bsonType: "string",
                      description: "Debe ser un string y es obligatorio"
                   },
                   telefono: {
                      bsonType: "string",
                      pattern: "^[0-9]{10}$",
                      description: "Debe ser un string de exactamente 10 dígitos y es obligatorio"
                   },
                   preferencias: {
                      bsonType: "array",
                      minItems: 1,
                      items: {
                         bsonType: "string",
                         description: "Debe ser un array de strings"
                      },
                      description: "Si se proporciona, debe tener al menos un elemento"
                   }
                },
                description: "Debe ser un objeto con dirección y teléfono obligatorios"
             }
          }
       }
    },
    validationLevel: "strict",
    validationAction: "error"
 });
```
#### Ejemplos de Validación con `pattern` en MongoDB

A continuación, se presentan algunos ejemplos de validación en MongoDB utilizando el campo `pattern` para aplicar expresiones regulares en diferentes formatos de datos.

```javascript
telefono: {
   bsonType: "string",
   pattern: "^\\+[0-9]{1,3}[0-9]{7,12}$"
}

email: {
   bsonType: "string",
   pattern: "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$"
}

codigo_postal: {
   bsonType: "string",
   pattern: "^[0-9]{5}$"
}

password: {
   bsonType: "string",
   pattern: "^(?=.*[a-z])(?=.*[A-Z])(?=.*\\d)(?=.*[@$!%*?&])[A-Za-z\\d@$!%*?&]{8,}$"
}

fecha: {
   bsonType: "string",
   pattern: "^(\\d{4})-(0[1-9]|1[0-2])-(0[1-9]|[12][0-9]|3[01])$"
}

nombre: {
   bsonType: "string",
   pattern: "^[a-zA-Z ]+$"
}

username: {
   bsonType: "string",
   pattern: "^[a-zA-Z0-9_]{3,15}$"
}

```


## Conclusión
v