# AggregationFramework

## Introducción
### Ejemplo introductorio
**Links de apoyo**

[Repositorio de Data de ejemplo](https://github.com/carlos-olivera/mongo_db_w_sample_db)

[Apoyo teórico](https://www.mongodb.com/docs/v7.0/core/databases-and-collections/)

Hablar de bases de dato tipo documental en MongoDB es hablar de la flexibilidad que tiene cada documento y cómo se pueden llegar a hacer subdocumentos complejos por los tipos de datos que se pueden llegar a utilizar. Dentro de este mar de información embebida o relacionada, pueden llegar a aparecer un sin fin de preguntas que en teoría nosotros tenemos que ayudar a responder. El responder preguntas complejos nos hacer consultas cada vez más específicas, por lo cual pensar en este flujo de trabajo nos debe llevar a los siguientes conceptos. 

Pipeline. Este suministro de datos o canalización de datos es una analogía de una cadena de montaje, que se divide por estapas, ejemplo:
- Filtrar 
- Transformar
- Preparar

**¿Una pregunta qué responder?** Obtener la media de edad de usuarios que realizaron compras en una tienda especifica de london, agrupar esa consulta por tipo de compra, si fueron presenciales, online o por telefono, ordenado por edad de menor a mayor.

Usar el dataset que viene en [Repositorio](https://github.com/carlos-olivera/mongo_db_w_sample_db)

```js
use("sample_supplies")
//Este entorno de consulta se realiza por estapas
//([{},{},{},{}])
db.sales.aggregate([
    //El signo de peso indica una referencia a un campo definido
    //$match:filtrar
    {
        //Acotamos las ventas solo para tiendas en Londres
        $match: {
          "storeLocation": "London"
        }
    },
    //$group:agrupar
    {
        //Una vez acotado las ventas a londres
        //Esas ventas las agrupamos por el canal de venta
        $group: {
          _id: "$purchaseMethod",
          //Una vez agrupado, qué operación debe hacer con esa información
          avgAge: {
            //Hacer referencia al objeto que guarda la información
            $avg: "$customer.age"
          }
        }
    },
    //$Projection:modelar
    {
        //Nos permite indicar que campos y de que forma queremos mostrar el resultado final
        $project: {
          _id: 0,
          metodo:"$_id",//Es el método al cual hemos hecho referencia
          MediaDeEdad:{
            $round:["$avgAge"]//Redondeo
          }
        }
    },
    //$sort:ordenar
    {
        $sort: {
          "MediaDeEdad": 1 //Criterio de orden
          //1 Acendente
          //-1 Descendente
        }
    }  
])
```
### Casos para usar Aggregation Framework y no queries de MongoDB o map-reduce

Para decidir esto es primordial entender los temas de costos y optimización, si se puede resolver con queries de MongoDB, es mejor realizarlo así, porque Aggregation Framework implica costos diferentes un performans diferente, por lo qué es importante analizar correctamente el alcance de cada herramienta.

**Ejemplo**

**¿Cuántos documentos de Airbnb corresponden a un rango de fechas específico?** El query tiene como objetivo contar los documentos que caen dentro de las fechas inicial y final proporcionadas. Esto se puede realizar utilizando tanto el estándar Query Language con el operador find, como el Aggregation Framework con etapas como match, group y project.

Otra opción que puediera servir para ciertas versiones de mongodb, sería MapReduce es un método para procesar grandes volúmenes de datos en colecciones, dividiendo el trabajo en dos etapas: map, que aplica una función a cada documento para emitir pares clave-valor, y reduce, que combina estos valores en un resultado agregado. Aunque flexible y capaz de manejar cálculos complejos mediante funciones personalizadas en JavaScript, es menos eficiente que el Aggregation Framework y está recomendado solo en casos específicos donde las operaciones estándar no sean suficientes o se requiera compatibilidad con sistemas antiguos.

**queries de MongoDB**
```js
use("sample_airbnb")
db.listingsAndReviews.find({
    last_review: {
        $gte: new Date("2019-01-01"),
        $lte: new Date("2020-01-01")
    }
})//.count()
```
**AggregationFramework**
```js
use("sample_airbnb")
db.getCollection('listingsAndReviews').aggregate([
    {$match: {
        last_review: {
            $gte: new Date('2019-01-01'),
            $lt: new Date('2020-01-01')
        }
    }},
    {$group: {
      _id: null,
      Total: {
        $sum: 1
      }
    }},
    {$project: {
      _id: 0
    }}
])
```
**Cuándo usar `db.collection.aggregate` vs `db.getCollection('collectionName').aggregate`**

| **Caso**                                   | **Usa `db.collection.aggregate`**  | **Usa `db.getCollection('collectionName').aggregate`** |
|--------------------------------------------|------------------------------------|--------------------------------------------------------|
| Nombre de la colección es estándar         | ✅                                 | ❌                                                    |
| Nombre contiene caracteres especiales      | ❌                                 | ✅                                                    |
| Nombre de la colección es dinámico         | ❌                                 | ✅                                                    |
| Prefieres un estilo más directo y legible  | ✅                                 | ❌                                                    |

---

Para fines prácticos, db.collection.aggregate y db.getCollection('collectionName').aggregate son funcionalmente lo mismo, ya que ambos ejecutan el framework de agregación sobre una colección. La única diferencia es que db.getCollection se usa cuando el nombre de la colección es dinámico o contiene caracteres especiales, mientras que db.collection.aggregate es más directo y se usa para nombres estándar. Si el nombre de la colección no tiene restricciones, puedes usar cualquiera.


## Operaciones
### $match

El operador $match puede llegar a tener similitudes a la clausula de consulta SQL **WHERE**, mediante el recurso de AggregationFramework es combeniente utilizar **$match** en los primeros pasos de nuestra consulta, si bien se puede acotar el universo de datos indistintamente en cualquier paso de la consulta para nuestro pipeline, al hacerlo en un inicio hacemos consultas eficientes que reducen el poder requerido de computo, no es lo mismo hacer una operación para un billón de datos que únicamente para 400 documentos, entonces desde el inicio si la consulta lo permite es bueno utilizar esta función.

**NUESTRO PRIMER PIPELINE**

Recuperar las 5 propiedades más económicas de estados unidos

![Pipeline 1](/A04.AggregationFramework/A04.AggregationFramework-Imagenes/Pipeline1.png)

La operación siguiente se puede realizar también en mongoDB query language
```js
use("sample_airbnb")
db.getCollection("listingsAndReviews").aggregate([
    //$match
    {
        $match: {
            //Solo en estados unidos
            "address.country_code": "US"
        }
    },
    //$sort
    {
        //ordenar de la más económica a la más costosa
        $sort: {
          price: 1
        }
    },
    //$limit
    {
        //Mostrar únicamente los primero 5 elementos del consulta
        $limit: 5
    },
    {
      //Para no ver tanta información limitamos a dos campos
      $project: {
        _id: 0,
        price: 1,
        name: 1
      }
    }
])
```
### $gruop
El operador $group es exclusivo del Aggregation Framework. Este operador es fundamental para realizar operaciones de agrupamiento y acumulación en las colecciones, similar a cómo funciona GROUP BY en SQL. Sin embargo, no está disponible en consultas normales como las realizadas con find(). El Aggregation Framework está diseñado específicamente para realizar análisis y transformaciones de datos más complejas que las que permiten las operaciones estándar de consulta (find, insert, update, etc.). Operaciones como sumar, agrupar o calcular promedios requieren pasos adicionales que el framework puede manejar eficientemente.

```js
use("sample_airbnb")
db.getCollection("listingsAndReviews").aggregate([
    //$match primera etapa de pipeline
    {
        $match: {
            //Solo en estados unidos
            "address.country_code": "US"
        }
    },
    //$sort segunda etapa del pipeline
    {
        //ordenar de la más económica a la más costosa
        $sort: {
            property_type: 1,
            price: 1
        }
    },
    //$Group
    {
        $group: {
            //Primera etapa el _id es el ancla sobre el cual tiene que trabajar
            _id: "$property_type",
            //Segunda etapa, que hacer una vez agrupados los datos
            //Para esto vamos a crear un objeto que nos guarde está información
            MásVarato: {
              $first: {
                    nombre: "$name",
                    precio: "$price",
                    dirección: "$address"
              }  
            },
            MásCaro: {
                $last: {
                    nombre: "$name",
                    precio: "$price",
                    dirección: "$address"
                }
            }
        }
    }
])
```

Lo interesante de nuestro operador group es que no se modifica nada dentro dela base de datos, pero modific a la forma de presentar la información de la misma para compartirla de acuerdo con los requerimientos.
### $project
Modelar los resultados, nos permite presentar la información en el formato que lo requiera el cliente.

![Modelado de datos](/A04.AggregationFramework/A04.AggregationFramework-Imagenes/modelado%20de%20datos.png)

```js
use("sample_airbnb")
db.getCollection("listingsAndReviews").aggregate([
    {
        $match: {
            //Solo en estados unidos
            "address.country_code": "US"
        }
    },
    //$sort
    {
            $sort: {
            property_type: 1,
            price: 1
        }
    },
    //$Group
    {
        $group: {
            _id: "$property_type",
            MásVarato: {
              $first: {
                    nombre: "$name",
                    precio: "$price",
                    dirección: "$address"
              }  
            },
            MásCaro: {
                $last: {
                    nombre: "$name",
                    precio: "$price",
                    dirección: "$address"
                }
            }
        }
    },
    //Darle formato a la información de salida
    {
        $project: {
            //con "$_id" estamos referenciado a la llave property_type
            "_id": 0, //Para que este campo que por defecto aparce, no se muestre
            "Tipo de propiedad":"$_id",
            "Menor precio": "$MásVarato.precio",
            "Mayor precio": "$MásCaro.precio"
        }
    }
])
```

![Etapas 1](/A04.AggregationFramework/A04.AggregationFramework-Imagenes/project.png)

Otra respuesta para homologar el resultado a lo que requiere la imagen de requeriemiento de formato.Uso de $toDouble en $project: Convierte los valores de Decimal128 a números simples (double), eliminando la estructura con "$numberDecimal".

```js
use("sample_airbnb")
db.getCollection("listingsAndReviews").aggregate([
    {
        $match: {
            //Solo en estados unidos
            "address.country_code": "US"
        }
    },
    //$sort
    {
            $sort: {
            property_type: 1,
            price: 1
        }
    },
    //$Group
    {
        $group: {
            _id: "$property_type",
            MásVarato: {
              $first: {
                    nombre: "$name",
                    precio: "$price",
                    dirección: "$address"
              }  
            },
            MásCaro: {
                $last: {
                    nombre: "$name",
                    precio: "$price",
                    dirección: "$address"
                }
            }
        }
    },
    //Darle formato a la información de salida
    {
        $project: {
            //con "$_id" estamos referenciado a la llave property_type
            "_id": 0, //Para que este campo que por defecto aparce, no se muestre
            "Tipo de propiedad":"$_id",
            "Menor precio": {$toDouble: "$MásVarato.precio"},
            "Mayor precio": {$toDouble: "$MásCaro.precio"}
        }
    }
])
```
### $count y $avg
En mongoDB puede llegar a ser complejo el uso de operadores, dado que son muchos, y puede llegar a ser complejo saber cuando usarlos.

Por lo general count y avg se utilizan dentro de group, similar a las funciones que se usan de manera generalizada con **gruop by en sql**.

**¿Por qué $sum: 1 para contar?**

1. $group acumula valores: Dentro de $group, se usan operadores como $sum, $avg, $min, $max, etc., para realizar cálculos sobre los documentos que pertenecen a cada grupo. $sum: 1 funciona sumando "1" por cada documento en el grupo, lo que equivale a contar los documentos en dicho grupo.

2. El operador $count es independiente: Aunque existe un operador llamado $count, este solo se usa fuera de $group, como una etapa independiente del pipeline para contar el total de documentos resultantes de las etapas anteriores. Dentro de $group, necesitas operadores de acumulación como $sum.

```js
use("sample_airbnb")
use("sample_airbnb")
db.getCollection("listingsAndReviews").aggregate([
    {
        $match: {
            //Solo en estados unidos
            "address.country_code": "US"
        }
    },
    //$sort
    {
            $sort: {
            property_type: 1,
            price: 1
        }
    },
    //$Group
    {
        $group: {
          _id: "$property_type",
          count: {
            $sum: 1                                
          },
          PrecioMedia: {
            $avg: "$price"
          },
          MásVarato: {
            $first: {
              nombre: "$name",
              precio: "$price",
              dirección: "$address"
            }  
          },
          MásCaro: {
            $last: {
              nombre: "$name",
              precio: "$price",
              dirección: "$address"
              }
          }
        }
    },
    //Darle formato a la información de salida
    {
      $project: {
        "_id": 0, 
        "Tipo de propiedad":"$_id",
        "Total de tipo de propiedad": "$count",
        "Precio medio de propiedades": {$toDouble: "$PrecioMedia"},
        "Menor precio": {$toDouble: "$MásVarato.precio"},
        "Mayor precio": {$toDouble: "$MásCaro.precio"}
      }
    }
])
```
### $set
Este operador se utiliza para dar formato igual que el $project, **¿Por qué tenemos otro operador?**  partiendo que project se tienen que denotar los capos que se quieran ver de manera explicita, esto no ocurre cuando usamos el operador $set, incluye todos los campos que ya estaban desde la última etapa de nuestro pipeline. 

**Comparación entre `$set` y `$project`**

| **Operador** | **Uso principal**                                                                                       | **Modifica documentos existentes** | **Selecciona o reestructura campos**          | **Ejemplo típico**                                                                                       |
|--------------|--------------------------------------------------------------------------------------------------------|------------------------------------|----------------------------------------------|---------------------------------------------------------------------------------------------------------|
| `$set`       | Agregar o modificar campos en los documentos existentes durante un pipeline.                           | ✅ Sí                             | ❌ No                                         | Añadir un campo calculado o actualizar uno existente: `{ $set: { total: { $sum: ["$price", "$tax"] } } }` |
| `$project`   | Seleccionar, renombrar, ocultar o transformar los campos en los documentos finales del pipeline.       | ❌ No                             | ✅ Sí                                         | Excluir un campo sensible o renombrar uno: `{ $project: { _id: 0, name: 1, totalPrice: "$total" } }`     |

---

**¿Cuándo usar `$set`?**
- Cuando necesitas **crear o modificar** un campo en los documentos.
- Ideal para agregar información adicional o realizar cálculos durante las etapas del pipeline.
- Es mas sencillo y es más optimo para tu tiempo utiliza `$set`

**¿Cuándo usar `$project`?**
- Si el documento original es bastante extenso y requieres formatearlo de manera fina

```js
use("sample_airbnb")
db.getCollection("listingsAndReviews").aggregate([
    {
        $match: {
            //Solo en estados unidos
            "address.country_code": "US"
        }
    },
    //$sort
    {
            $sort: {
            property_type: 1,
            price: 1
        }
    },
    //$Group
    {
        $group: {
            _id: "$property_type",
            count: {
                $sum: 1                                
            },
            PrecioMedia: {
                $avg: "$price"
            },
            MásVarato: {
              $first: {
                    nombre: "$name",
                    precio: "$price",
                    dirección: "$address"
              }  
            },
            MásCaro: {
                $last: {
                    nombre: "$name",
                    precio: "$price",
                    dirección: "$address"
                }
            }
        }
    },
    {
        $set: {
            "Tipo de propiedades": "$_id",          
            "Cantidad de propiedades": "$count",
            "Precio promedio": { $toDouble: {$round: ["$PrecioMedia",2]}},
            //Un array de documentos
          "Propiedades Destacadas": [
            {
                "Tipo": "Mas bajo",
                "Nombre": "$MásVarato.nombre",
                "Precio": {$toDouble:"$MásVarato.precio"},
            },
            {
                "Tipo": "Mas alto",
                "Nombre": "$MásCaro.nombre",
                "Precio": {$toDouble:"$MásCaro.precio"},
            }]
        }
    },
    {
        $project: {
            "_id": 0,
            "count":0, 
            "PrecioMedia": 0, 
            "MásVarato":0,
            "MásCaro":0,
            "dirección": 0
        }
    }
])
```

## Etapas de AggregationFramework
### $unwind
El operador $unwind en el Aggregation Framework de MongoDB descompone un campo de tipo array en múltiples documentos, generando un documento por cada elemento del array. Esto permite procesar y analizar los datos dentro de arrays como si fueran documentos individuales, facilitando operaciones como filtrado, agrupamiento y transformación. Es útil cuando necesitas tratar elementos de un array por separado o combinarlos con otras colecciones. También puedes controlar cómo manejar arrays vacíos o campos inexistentes utilizando las opciones `preserveNullAndEmptyArrays` y `includeArrayIndex`.

```js
use("sample_airbnb")
db.getCollection("listingsAndReviews").aggregate([
  //Desconponer el el array de amenities
  {$unwind: "$amenities"},
  //Una vez extraida esa información de los arrays
  //Se agrupan por amenities
  {$group: {
    _id: "$amenities",
    count: {
      $sum: 1
    }
  }},
  //Organizar del que se encuntra más al que se encuentra menos
  {$sort: {
    count: -1
  }},
  {$limit: 10},
  {$project: {
    _id:0,
    Amenidad: "$_id",
    Cantida: "$count",
    "Porcentaje presente":{
        //Es similar a python, operaciones anidadas que se resuelven de adentro hacia afuera
        //$literal nos indica que vamos usar una constante tal cual está
        $round: [{$multiply:[{$divide:["$count",{$literal: 5600}]},100]},2]
    }
  }}
])
```
### $out
Como almacenar los resultado en mongoDB, esto no queda en memoria física, sino solo memoria volatil. Para esto usamos $out, si queremos exportar el resultado de forma sencilla. Este operador debe ser la última etapa del aggregate, si el nombre es el mismo nombre de una colección que ya existe automaticamente la va sobreescribir, $out afecta el rendimiento, los indices no se realizan de manera automaticamente.

```js
use("sample_airbnb")
db.getCollection("listingsAndReviews").aggregate([
    {$sort: {
        "address.market": 1,
        price: -1
    }},
    {
        $group: {
            _id: "$address.market",
            masCostosas: {
                $first: {
                    name: "$name",
                    price: "$price"
                }
            }
        }
    } ,
    {
        $out: "propiedadesMasCostosas"
    }
])

db.propiedadesMasCostosas.find()
```
### $geoNear
En el caso de nuestra base de datos esta en un directorio nuestro indice de localización por lo cual es necesario crear un indice geoespacial para podertrabajar.

```js
use("sample_airbnb")
// db.listingsAndReviews.createIndex({ "address.location": "2dsphere" });

db.getCollection("listingsAndReviews").aggregate([
    //El resultado de esta función es un filtrado de los puntos a 30 kilometros a la redonda de nuestra referencia
    {$geoNear:{
     //Le ponemos de ferencia un valor de las cordenadas de un punto
        near: { type: 'Point', coordinates: [ -73.95810439176357, 40.800552066589546] },
        //Donde guardamos la distancia
        distanceField: 'distancia',
        //Máximo al rededor
        //geoNear trabaja con metro
        //30km
        maxDistance: 30000,
        //Es más exacto pero requieren más procesamiento
        spherical: true
    }},
    // {$count: 'total a $maxDistance'        
    // }
])
```
**Una consulta más específica**

```js
use("sample_airbnb")
// db.listingsAndReviews.createIndex({ "address.location": "2dsphere" });
db.getCollection("listingsAndReviews").aggregate([
    //El resultado de esta función es un filtrado de los puntos a 30 kilometros a la redonda de nuestra referencia
    {$geoNear:{
     //Le ponemos de ferencia un valor de las cordenadas de un punto
     near: { type: 'Point', coordinates: [ -73.95810439176357, 40.800552066589546] },
     //Donde guardamos la distancia
     distanceField: 'distancia',
     //Máximo al rededor
     //geoNear trabaja con metro
     //30km
     maxDistance: 30000,
     //Es más exacto pero requieren más procesamiento
     spherical: true
    }},
    //Consulta más compleja determinar la relación precio por zona en función de número de camas
    {$match: {
      beds: {
        //que sea diferente de 0
        //"not equal" (no igual)
        $ne: 0}
    }},
    //Por primera vez ponemos project antes que group
    {$project: {
     //No olvidar las comillas
     DistanciaEnReferencia: {$round: ["$distancia",2]},
     PrecioPorCama:{
       $toDouble:{$divide:["$price","$beds"]}
     }
    }}
    // Indicar una condicional, para obtener un resultado arbitrario
    ,{$group: {
        //Con este condicional estamos segregando en función de la distancia
      _id:{ $cond:{
                    if: {
                    //Si la distancia es mayor a 2 kilometros
                    $lte: ["$DistanciaEnReferencia", 2000]
                    },
                    then: "Menos de 2 Km",
                    else: "Más de 2 Km"
                    }
        },
        MediaPorCama: {
            $avg: "$PrecioPorCama"
        }
    }}
    // ,{
    //     $sort: {
    //       Distancia: -1
    //     }
    // }
])
```

**Un reto en este punto de los temas es revisar que caracteristicas hay en las propiedades que están cerca de las cosas. Hacer una comparativa.**
### $lookup
El rumor de que "no se pueden hacer uniones en MongoDB" surge porque MongoDB, al ser una base de datos NoSQL orientada a documentos, prioriza estructuras denormalizadas en lugar de relaciones tradicionales como en bases SQL. Sin embargo, el operador $lookup desmiente este rumor al permitir realizar uniones (joins) entre colecciones, similar a un SQL JOIN. $lookup toma documentos de una colección principal y los combina con documentos de otra colección basada en un campo común, facilitando relaciones uno a uno o uno a muchos, aunque su rendimiento puede ser menor en conjuntos de datos grandes si no están optimizados.

**¿Queremos optener el número de los clientes con mayor número de transacciones?**

```js
use("sample_analytics")
// db.listingsAndReviews.createIndex({ "address.location": "2dsphere" });

db.getCollection("customers").aggregate([ 
    //Desde customers estoy haciendo traer la información de accounts
    //En este primer lookup estamos creando un array que nos permite trabajar
    {$lookup: {
      //Especificamos de que colección estamos partiendo
      from: "accounts",
      //Indica el campo local de la primera colección
      localField: "accounts",    
      foreignField: "account_id",
      as: "account_info" //este es el array creado
    }},
    //Extraemos la información del array
    {$unwind:"$account_info"},
    
    //Ahora tenemos juntos información de costumers y account
    //Se junta más información con transactions
    {$lookup: {
        //Especificamos de que colección estamos partiendo
        from: "transactions",
        //Indica el campo local de la primera colección
        localField: "account_info.account_id",    
        foreignField: "account_id",
        as: "transactions_inf" //este es el array creado
      }},
      //Extraemos la información del array
    {$unwind:"$transactions_inf"},
    {$sort:{
      "transactions_inf.transaction_count": -1
    }},
    {$project: {
       "_id": 0,
       "Cliente": "$name",
       "Número de transacciones": "$transactions_inf.transaction_count"
    }},
    {$limit: 5}
])
```

## Operaciones avanzadas
### Funciones personalizadas $function
Las funciones personalizadas en MongoDB se pueden escribir en JavaScript, que es el único lenguaje de programación nativo soportado para este tipo de funciones dentro de la base de datos. MongoDB permite que las funciones personalizadas se ejecuten utilizando JavaScript en varias operaciones, especialmente dentro de los pipelines de agregación, con el operador $function.

Alcance de las funciones personalizadas
1. Cálculos complejos: Permiten ejecutar operaciones o cálculos que no están directamente soportados por las funciones estándar de MongoDB.
2. Operaciones personalizadas: Pueden realizar tareas específicas que se adapten a las necesidades del negocio, como transformaciones de datos complejas, manipulación de cadenas, cálculos estadísticos personalizados, etc.
3. Operación dentro de agregaciones: Se pueden usar dentro de un pipeline de agregación en MongoDB. Por ejemplo, en operaciones $project, $group, o $addFields, puedes usar funciones personalizadas para realizar transformaciones y cálculos en los datos.
4. Evitar operaciones externas: Reducen la necesidad de hacer post-procesamiento fuera de la base de datos (como en una aplicación cliente), lo cual puede mejorar el rendimiento y reducir la latencia, especialmente si se usan en operaciones agregadas.

Solo debería ocuparse las funciones de js cuando no esté cubierto el alcance de la consulta dentro de las herramientas nativas de mongodb, esto porque son costosas este tipo de consultar.

**Ejemplo**
```js
db.collection.aggregate([
  {
    $group: {
      _id: null,
      valores: { $push: "$value" }  // Suponiendo que "value" es el campo con los datos
    }
  },
  {
    $project: {
      desviacionEstandar: {
        $function: {
          body: function(valores) {
            // Calcular la desviación estándar en JavaScript
            var media = valores.reduce((a, b) => a + b, 0) / valores.length;
            var sum = valores.reduce((a, b) => a + Math.pow(b - media, 2), 0);
            return Math.sqrt(sum / valores.length);
          },
          args: ["$valores"],
          lang: "js"
        }
      }
    }
  }
])
```
MongoDB no tiene un operador nativo para calcular la desviación estándar dentro de los pipelines de agregación. Las funciones estadísticas estándar en MongoDB, como $avg, $sum, $min, etc., son limitadas a cálculos simples, pero para algo como la desviación estándar (que requiere manipulación de los datos y cálculo intermedio), es necesario usar una función JavaScript personalizada con $function.
### $redact
Este operador nos puede ahorrar tiempo al momento de filtrar, seleccionar, o excluir parte de nuestro documento. Este operador nos ayuda a discriminar información en relación a los subdocumentos en un punto específico de las etapas de aggregate. En MongoDB, el operador $redact se utiliza en el pipeline de agregación para filtrar y modificar documentos a un nivel muy detallado, basado en un criterio condicional que evalúa cada parte de un documento de manera recursiva. Este operador es especialmente útil cuando se requiere controlar el acceso a los datos o aplicar transformaciones complejas en los documentos.

**Ejemplo de una consulta que omita directamente todas las calificaciones en un reango específico**

```js
use("sample_airbnb")
db.getCollection("listingsAndReviews").aggregate([ 
    //Determinar la condición para acotar la información de los documetos
    {$redact: {
        $cond:{
            if:{$gte:["$review_scores.review_scores_rating", 95]},
                    //Valor especial del comando, mantiene el campo      
                    then: "$$KEEP",//Está función es parte de $react
                    //Qué quiero que hagas
                    else: "$$PRUNE"//Qué lo elimines, que no tome en cuenta este.
                }
    }},
    {$count: 'count'}
])
```
### $accumulator

El operador $accumulator en MongoDB permite ejecutar funciones de acumulación personalizadas dentro de un pipeline de agregación. A diferencia de los operadores de agregación estándar como $sum o $avg, $accumulator proporciona una estructura más flexible para realizar operaciones complejas mediante la definición de funciones de acumulación en cuatro partes clave: init, accumulate, merge y finalize. La clave init establece el valor inicial del acumulador, accumulate define cómo acumular datos a medida que se procesan los documentos, merge describe cómo combinar los acumuladores parciales en operaciones distribuidas, y finalize permite realizar una operación final sobre el valor acumulado antes de devolverlo. Este operador es útil cuando se requiere lógica más sofisticada que no puede lograrse con operadores predefinidos, como contar elementos bajo condiciones específicas, acumular valores de manera no estándar o realizar cálculos complejos en múltiples etapas del pipeline.

![Accumulator](/A04.AggregationFramework/A04.AggregationFramework-Imagenes/accumulatior.png)

**Determinar si entre más amenidades más calificación**

```js
db.listingsAndReviews.aggregate([
    {
        $match: {"review_scores.review_scores_rating": {$gt: 90}}
    }, 
    {
        $addFields: {
            amenitiesSize: {$size: "$amenities"} 
        }
    }, 
    {
        $group: {
            _id: "$review_scores.review_scores_rating",
            totalAmenities: {
                $sum: "$amenitiesSize"
            },
            totalProperties: {
                $sum: 1
            }
        }
    },
    {
        $sort: {
            _id: -1
        }
    },
    {
        $project: {
            property_score: "$_id",
            _id: 0,
            mediaAmenities: {
                $divide: ["$totalAmenities", "$totalProperties"]
            }
        }
    }
])

//con accumulator

var db=db.getSiblingDB("sample_airbnb")

db.listingsAndReviews.aggregate([
    {
        $match: {
            "review_scores.review_scores_rating": { $gte: 90},
        }
    },
    {
        //para guadar el acumulador en un campo
        $addFields:{
            amenitiesSize: {$size: "$amenities"}
        }
    },
    {
        $group: {
          _id: null,
          media: {
            $accumulator: {
                init: function(){
                    return {sum:0, count:0};
                },

                accumulateArgs:["$amenitiesSize"],

                accumulate: function(state, size){
                    return {sum: state.sum + size, count: state.count+1};
                },

                // se crea una funcion para juntar documentos
                merge: function(state1, state2){
                    return {
                        sum: state1.sum + state2.sum,
                        count: state1.count + state2.count
                    };
                },

                //finalizar
                finalize: function(state){
                    return state.sum /state.count
                },
                lang:"js"
          }
        }
      }
    }
]
);
```
### $bucket y $bucketAuto

Este código utiliza el operador $bucket en el pipeline de agregación para agrupar documentos de la colección listingsAndReviews según el campo review_scores.review_scores_rating. Define los límites (boundaries) para los grupos como [0, 50, 70, 85, 100], creando rangos de puntuaciones: [0-50), [50-70), [70-85), y [85-100). Los documentos cuyo valor de review_scores_rating no caen dentro de estos rangos se agrupan en un bucket predeterminado llamado "N/A". Cada bucket produce un campo de salida count, que cuenta cuántos documentos pertenecen a ese grupo. El resultado es una distribución de los documentos según sus puntajes de calificación.
```js
// bucket.mongodb

use('sample_airbnb')

db.listingsAndReviews.aggregate([
    {
        $bucket: {
            groupBy: "$review_scores.review_scores_rating",
            boundaries: [0, 50, 70, 85, 100],
            default: "N/A",
            //Aquí le indicamos lo que va a la salida de la siguiente etapa
            output: {
                count: {$sum: 1}
            }
        }
    }
])
```

Este código utiliza el operador $bucketAuto para agrupar documentos de la colección listingsAndReviews de manera automática según el campo review_scores.review_scores_rating. A diferencia de $bucket, no requiere definir límites específicos; en su lugar, se especifica el número de grupos deseados (buckets: 4). MongoDB divide automáticamente los valores de review_scores_rating en 4 rangos aproximadamente iguales en tamaño (en términos de distribución de documentos). Para cada grupo, genera un campo count, que cuenta cuántos documentos pertenecen a ese rango. Esto es útil cuando no se conocen los límites exactos pero se desea segmentar los datos en un número específico de grupos.

```js
/// BucketAuto.mongodb

use('sample_airbnb')

db.listingsAndReviews.aggregate([
    {
        $bucketAuto: {
            groupBy: "$review_scores.review_scores_rating",
            buckets: 4,
            //Aquí le indicamos lo que va a la salida de la siguiente etapa
            output: {
                count: { $sum: 1 }
            }
        }
    }
])
```

## Performance(Desempeño) y Optimización
### Otimización y uso de recursos
- No es lo mismo modelar que consultar millones y millones de registros;
- Primero planea tu agregación, qué etapa necesitas, si es posible en papel, para depués implementarlo.
- Considerar si realmente es necesario usar aggregationframework, ver que recursos puedo usar antes de usarlo.
- El orde de cada etapa, el $match es recomendable hacerlo primero, es importante tener en mete que cada etapa reciba lo que necesita nada vez.
- Cuidado con los Array, que no sean muy extensos, puede ocupar mucho espacio.
- Funciones personalizadas solo cuando es necesario.
- Consultar datos reales para consultar los recursos de las consultas, para valdiar lo que si es cuantitativo.
### Profiling

Hay un comando para analizar el performance mediante estadísticas, para comparar consultas.

![Profiling](/A04.AggregationFramework/A04.AggregationFramework-Imagenes/profiling.png)

```js
{
  "explainVersion": "1",
  "stages": [
    {
      "$cursor": {
        "queryPlanner": {
          "namespace": "sample_analytics.customers",
          "indexFilterSet": false,
          "parsedQuery": {},
          "queryHash": "325EA78D",
          "planCacheKey": "325EA78D",
          "maxIndexedOrSolutionsReached": false,
          "maxIndexedAndSolutionsReached": false,
          "maxScansToExplodeReached": false,
          "winningPlan": {
            "stage": "PROJECTION_DEFAULT",
            "transformBy": {
              "account_info.account_id": 1,
              "accounts": 1,
              "name": 1,
              "transactions_inf.transaction_count": 1,
              "_id": 0
            },
            "inputStage": {
              "stage": "COLLSCAN",
              "direction": "forward"
            }
          },
          "rejectedPlans": []
        },
        "executionStats": {
          "executionSuccess": true,
          "nReturned": 500,
          "executionTimeMillis": 1932,
          "totalKeysExamined": 0,
          "totalDocsExamined": 500,
          "executionStages": {
            "stage": "PROJECTION_DEFAULT",
            "nReturned": 500,
            "executionTimeMillisEstimate": 1,
            "works": 501,
            "advanced": 500,
            "needTime": 0,
            "needYield": 0,
            "saveState": 1,
            "restoreState": 1,
            "isEOF": 1,
            "transformBy": {
              "account_info.account_id": 1,
              "accounts": 1,
              "name": 1,
              "transactions_inf.transaction_count": 1,
              "_id": 0
            },
            "inputStage": {
              "stage": "COLLSCAN",
              "nReturned": 500,
              "executionTimeMillisEstimate": 0,
              "works": 501,
              "advanced": 500,
              "needTime": 0,
              "needYield": 0,
              "saveState": 1,
              "restoreState": 1,
              "isEOF": 1,
              "direction": "forward",
              "docsExamined": 500
            }
          }
        }
      },
      "nReturned": 500,
      "executionTimeMillisEstimate": 1
    },
    {
      "$lookup": {
        "from": "accounts",
        "as": "account_info",
        "localField": "accounts",
        "foreignField": "account_id",
        "unwinding": {
          "preserveNullAndEmptyArrays": false
        }
      },
      "totalDocsExamined": 871254,
      "totalKeysExamined": 0,
      "collectionScans": 499,
      "indexesUsed": [],
      "nReturned": 1748,
      "executionTimeMillisEstimate": 406
    },
    {
      "$lookup": {
        "from": "transactions",
        "as": "transactions_inf",
        "localField": "account_info.account_id",
        "foreignField": "account_id",
        "unwinding": {
          "preserveNullAndEmptyArrays": false
        }
      },
      "totalDocsExamined": 3050262,
      "totalKeysExamined": 0,
      "collectionScans": 1747,
      "indexesUsed": [],
      "nReturned": 1752,
      "executionTimeMillisEstimate": 1930
    },
    {
      "$sort": {
        "sortKey": {
          "transactions_inf.transaction_count": -1
        },
        "limit": 5
      },
      "totalDataSizeSortedBytesEstimate": 450510,
      "usedDisk": false,
      "spills": 0,
      "nReturned": 5,
      "executionTimeMillisEstimate": 1931
    },
    {
      "$project": {
        "Cliente": "$name",
        "Número de transacciones": "$transactions_inf.transaction_count",
        "_id": false
      },
      "nReturned": 5,
      "executionTimeMillisEstimate": 1931
    }
  ],
  "serverInfo": {
    "host": "e9e14ddec85f",
    "port": 27017,
    "version": "6.0.19",
    "gitVersion": "a7ada5ff3a4d8a1e2ed88f86bd6b3d1d16cb43c6"
  },
  "serverParameters": {
    "internalQueryFacetBufferSizeBytes": 104857600,
    "internalQueryFacetMaxOutputDocSizeBytes": 104857600,
    "internalLookupStageIntermediateDocumentMaxSizeBytes": 104857600,
    "internalDocumentSourceGroupMaxMemoryBytes": 104857600,
    "internalQueryMaxBlockingSortMemoryUsageBytes": 104857600,
    "internalQueryProhibitBlockingMergeOnMongoS": 0,
    "internalQueryMaxAddToSetBytes": 104857600,
    "internalDocumentSourceSetWindowFieldsMaxMemoryBytes": 104857600
  },
  "command": {
    "aggregate": "customers",
    "pipeline": [
      {
        "$lookup": {
          "from": "accounts",
          "localField": "accounts",
          "foreignField": "account_id",
          "as": "account_info"
        }
      },
      {
        "$unwind": "$account_info"
      },
      {
        "$lookup": {
          "from": "transactions",
          "localField": "account_info.account_id",
          "foreignField": "account_id",
          "as": "transactions_inf"
        }
      },
      {
        "$unwind": "$transactions_inf"
      },
      {
        "$sort": {
          "transactions_inf.transaction_count": -1
        }
      },
      {
        "$project": {
          "_id": 0,
          "Cliente": "$name",
          "Número de transacciones": "$transactions_inf.transaction_count"
        }
      },
      {
        "$limit": 5
      }
    ],
    "cursor": {},
    "$db": "sample_analytics"
  },
  "ok": 1
}
```

1. $lookup:
Uso: Une documentos de dos colecciones.
from: Especifica la colección secundaria.
localField y foreignField: Relacionan los documentos entre las dos colecciones.
as: Define el campo donde se almacenará el resultado de la unión.
Importancia: Ideal para consultas tipo "join". En este caso, une customers con accounts y luego con transactions.
2. $unwind:
Uso: Desnormaliza arreglos, generando un documento separado por cada elemento del arreglo.
Importancia: Es necesario después de $lookup para procesar cada transacción o cuenta de forma independiente.
3. $sort:
Uso: Ordena documentos por uno o más campos.
sortKey: Define el campo y el orden (1 para ascendente, -1 para descendente).
Importancia: Aquí se utiliza para ordenar por el número de transacciones en orden descendente.
4. $project:
Uso: Selecciona o transforma campos en el documento de salida.
Ejemplo: Cambia el nombre de name a Cliente y transactions_inf.transaction_count a Número de transacciones.
5. executionStats:
nReturned: Número de documentos devueltos.
executionTimeMillis: Tiempo de ejecución total.
docsExamined: Cantidad de documentos examinados en la colección.
Importancia: Ayuda a identificar cuellos de botella, como un COLLSCAN en lugar de un índice.
6. COLLSCAN:
Uso: Escanea toda la colección, ya que no se usaron índices.
Problema: Es menos eficiente para consultas grandes. Crear índices relevantes podría optimizar la ejecución.
7. $bucket y $bucketAuto:
Uso: Agrupan documentos en rangos definidos ($bucket) o calculados automáticamente ($bucketAuto).
Importancia: No aparece en este pipeline, pero es útil para crear distribuciones estadísticas en otros contextos.
8. Parámetros del servidor:
internalDocumentSourceGroupMaxMemoryBytes: Límite de memoria para operaciones como $group.
internalQueryMaxBlockingSortMemoryUsageBytes: Límite de memoria para $sort.





