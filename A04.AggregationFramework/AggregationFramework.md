# AggregationFramework
## Introducción
### Ejmplo introductorio
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

