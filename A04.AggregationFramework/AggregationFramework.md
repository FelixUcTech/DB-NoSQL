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


