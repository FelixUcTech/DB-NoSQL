# Fundamentos

## Bases de datos NoSQL (Not Only SQL)

Las bases de datos NoSQL su escalamiento es más sencillo que de una base de datos SQL, además hay que tomar en cuenta que también importa que estrategía de escalamiento tome uno para aumento de capacidades, también las bases de datos NoSQL son buenas para la replicación.

**Escalamiento vertical**

Si se llega a caer el servidor, por más memoria ram que le pongo o capacidad de procesamiento, es el único que tengo, y si se va fuera de operación no tengo otro.

![Escalamiento-Vertical](/A01.Fundamentos/A01.Fundamentos-Imagenes/escalamiento-vertical.png)

**Escalamiento horizontal**

Nos permite una alta disponibildad, dado que cómo maquinas (procesadores) virtuales, si llegara a caerse este servidor, uno puede tomar el lugar de caido y replicar, por ello es que tienen mayor disponibilidad.

![Escalamiento-Horizontal](/A01.Fundamentos/A01.Fundamentos-Imagenes/escalamiento-horizontal.png)


Comparación de costos a lo largo del tiempo

![Escalamiento-comparacion](/A01.Fundamentos/A01.Fundamentos-Imagenes/escalamiento-comparacion.png)

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
