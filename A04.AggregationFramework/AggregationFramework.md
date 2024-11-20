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
