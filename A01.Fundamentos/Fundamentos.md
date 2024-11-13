# Fundamentos

## Archivo .editorconfig
El archivo .editorconfig establece reglas de formato para varios tipos de documentos, como archivos de código fuente (JS, Python, etc.), HTML, CSS, y otros. Su alcance incluye la configuración de espacios, tabulaciones y finales de línea, facilitando la consistencia del estilo en proyectos colaborativos.


## Bases de datos NoSQL (Not Only SQL)

Las bases de datos NoSQL su escalamiento es más sencillo que de una base de datos SQL, además hay que tomar en cuenta que también importa que estrategía de escalamiento tome uno para aumento de capacidades, también las bases de datos NoSQL son buenas para la replicación.

**Escalamiento vertical**

Si se llega a caer el servidor, por más memoria ram que le pongo o capacidad de procesamiento, es el único que tengo, y si se va fuera de operación no tengo otro.

![Escalamiento-Vertical](/A01.Fundamentos/A01.Fundamentos-Imagenes/escalamiento-vertical.png)

**Escalamiento horizontal**

Nos permite una alta disponibildad, dado que cómo maquinas (procesadores) virtuales, si llegara a caerse este servidor, uno puede tomar el lugar de caido y replicar, por ello es que tienen mayor disponibilidad.

![Escalamiento-Horizontal](/A01.Fundamentos/A01.Fundamentos-Imagenes/escalamiento-horizontal.png)


**Comparación de costos a lo largo del tiempo**

![Escalamiento-comparacion](/A01.Fundamentos/A01.Fundamentos-Imagenes/escalamiento-comparacion.png)

**Replicación**

![replicacion-topologia](/A01.Fundamentos/A01.Fundamentos-Imagenes/replicacion-topologia.png)

### Bases de Datos Documentales
En estas bases de datos se empareja cada clave con una estructura de datos compleja que de denomina 'documento'. Este tipo de bases de datos documentales emparejan los documentos con una estructura **"clave - valor"**, como los JSON, que son guardadas en colecciones.

*Ejemplo de estas bases de datos son*
        - Mongo DB
        - Cloud Firestore
        - Couchbase
        - Etc.

### Bases de datos orientadas a gráfos
Se utilizan par alamacena información sobre redes de datos, como las conexiones sociales, como nota: **Las bases de datos de grafos como Nqo4j son más eficientes para ciertas consultas comparado con las bases de datos SQL tradicionales porque las conexiones nativas en grafos permiten consultas reápidas directas sin necesidad de realizar joins complejos**, ejemplo de esto:

#### Contexto
Supongamos que estamos trabajando con una red social, y queremos encontrar las conexiones de segundo grado de un usuario (es decir, "amigos de amigos").

En una base de datos SQL tradicional, tendríamos que hacer varios `JOINs` en una tabla de relaciones para identificar las conexiones, lo cual puede hacer la consulta lenta y costosa, especialmente en redes de gran tamaño. En cambio, en una base de datos de grafos como Neo4j, podemos realizar esta consulta de forma directa aprovechando las conexiones nativas.

#### Estructura en Neo4j

1. **Modelo de Datos**: En Neo4j, los usuarios están almacenados como nodos, y las relaciones de amistad entre ellos se representan como aristas.

2. **Consulta de Amigos de Amigos**: Para encontrar las conexiones de segundo grado, podemos utilizar la siguiente consulta en Cypher (el lenguaje de consultas de Neo4j):

``` cypher
MATCH (usuario:Persona {nombre: "Juan"})-[:AMIGO]->(amigo)-[:AMIGO]->(amigoDeAmigo)
WHERE amigoDeAmigo <> usuario
RETURN amigoDeAmigo.nombre AS AmigosDeAmigos
```
### Clave Valor
Son las bases de datos NoSQL más simples. Cada elemento de la base de datos se almacena como un nombre de atributo (o <<calve>>), junto con su valor.

*Ejemplo de estas bases de datos son*
        - Redis
        - Etc.

### Orientadas a Columnas
Estas bases de datos, como Cassandra o HBas, permiten realizar consultas en grandes conjuntos de datos y almacenan los datos en columnas, en lugar de filas.


## ¿Cuando usar Bases de datos relaciones y cuando no?


## ¿Qué tecnología debo usar SQL & NoSQL?
Siempre depende del caso de uso, pero es díficil decir que una tecnología puede solventar todas las necesidades, así que cuando hablamos de multiples tecnologías es muy probable que no haya solamente SQL como leguaje predeterminante. A esto se le conoce como persistencia pologlota.

- Ventajas NoSQL
    - Datos indeterminados
    - Semi estructurados
    - No relacionados
    - Distribución geográfica
    - Datos definidos por terceros

Podría funcionar bien para un sistema bancario, porque ya se tiene un esquema definido y la consistencia del modelo acid nos pemite mantener la integridad de la información.

- Ventajas SQL
    - Datos definidos
    - Relaciones claras entre datos
    - A.C.I.D.

No hay que estar casado con una sola tecnología, hay que estar abiertos a usarlas cuando es necesario.

## ¿Qué son las APIs?
Una API (Interfaz de Programación de Aplicaciones, por sus siglas en inglés) es un conjunto de reglas y protocolos que permite que diferentes aplicaciones o servicios se comuniquen entre sí. Las APIs definen cómo se deben estructurar las solicitudes y respuestas entre sistemas, facilitando el intercambio de datos o la ejecución de funciones entre aplicaciones sin necesidad de conocer sus detalles internos.

Por ejemplo, una API puede permitir que una aplicación móvil acceda a los datos de una base de datos en la nube o que dos servicios web intercambien información.

## ¿Qué es un data pipline?

Un data pipeline (o canalización de datos) es un conjunto de procesos y herramientas que permiten mover, transformar y almacenar datos desde una fuente (o múltiples fuentes) hacia un destino, generalmente un sistema de almacenamiento o análisis, de manera automatizada y eficiente. Los pipelines de datos son fundamentales en el procesamiento de grandes volúmenes de datos, ya que permiten integrar, limpiar, transformar y cargar los datos para su posterior análisis.

Componentes clave de un Data Pipeline
1. Fuente de datos: Los datos pueden provenir de diversas fuentes, como bases de datos, archivos, APIs, servicios web, sensores, entre otros.

2. Ingesta de datos: Este es el proceso de extraer datos de las fuentes y prepararlos para ser procesados. Se puede hacer de manera en tiempo real (streaming) o en lotes (batch).

3. Transformación de datos: Aquí, los datos extraídos son transformados de acuerdo con los requisitos específicos del análisis o almacenamiento. Esto puede incluir:

    - Limpieza: Eliminar valores erróneos o incompletos.

    - Conversión: Convertir los datos de un formato a otro (por ejemplo, de CSV a JSON).

    - Agregación: Resumir los datos, por ejemplo, promediando valores o sumando totales.
    
    - Enriquecimiento: Combinar los datos con otras fuentes de información para mejorar su valor.

4. Almacenamiento de datos: Después de ser procesados, los datos se almacenan en un sistema de almacenamiento adecuado, como bases de datos, data lakes (lagos de datos) o almacenes de datos (data warehouses).

5. Consumo de datos: Finalmente, los datos almacenados pueden ser consultados y utilizados para análisis, informes, visualizaciones o para alimentar otros sistemas.

Tipos de Data Pipelines
1.  Pipelines en tiempo real (streaming): Estos pipelines procesan datos a medida que llegan. Se usan para escenarios donde los datos necesitan ser procesados y analizados inmediatamente, como el monitoreo en tiempo real o las recomendaciones personalizadas.

2. Pipelines por lotes (batch): Este tipo de pipeline procesa grandes volúmenes de datos en intervalos definidos, como una vez al día, o en lotes programados. Son útiles cuando los datos no necesitan ser procesados de inmediato.

3. Pipelines híbridos: Combinan ambos enfoques, procesando algunos datos en tiempo real y otros en lotes, dependiendo de la necesidad.

Ejemplo de un Data Pipeline
Un ejemplo básico de un pipeline de datos podría ser el siguiente:

1. Ingesta: Extraer datos de un archivo CSV que contiene ventas de productos.
Transformación: Limpiar los datos (eliminar registros con valores nulos), agregar las ventas por producto, y convertir los valores de fecha a un formato uniforme.
2. Almacenamiento: Cargar los datos transformados en un data warehouse (por ejemplo, Amazon Redshift o Google BigQuery).
3. Consumo: Los analistas consultan los datos almacenados para generar informes sobre las ventas por producto.

Herramientas Comunes para Data Pipelines
- Apache Kafka: Para procesamiento de datos en tiempo real.
- Apache Airflow: Para orquestar y automatizar pipelines por lotes.
- Apache NiFi: Para integrar y mover datos entre sistemas.
- AWS Glue: Para transformaciones de datos en la nube de AWS.
- Google Dataflow: Para procesamiento de datos en la nube de Google.
- Talend: Plataforma para la integración y transformación de datos.

Beneficios de un Data Pipeline
- Automatización: Permite la ejecución automática de tareas de procesamiento de datos sin intervención manual.
- Escalabilidad: Los pipelines pueden manejar grandes volúmenes de datos, escalando según sea necesario.
- Eficiencia: Mejoran la eficiencia operativa al permitir el procesamiento de datos en tiempo real o por lotes.
- Consistencia: Aseguran que los datos se procesen de manera consistente y sin errores.