# Mongo DB

Mongo DB representa el 45% del mercado en bases de datos a Julio del 2024, al igual que algunos servicios privados como Azure Cosmos DB, conecta con ciertas herramientas. Es un poliglota persistente.

Mongo DB guarda los datos en formato BSON (binary JSON)

Atlas se puede llegar a considerar cómo un poliglota persistente. MongoDB Atlas es una plataforma de base de datos en la nube gestionada para bases de datos MongoDB. Permite crear, desplegar y escalar bases de datos MongoDB sin preocuparse por la infraestructura, ya que gestiona automáticamente el rendimiento, la seguridad y las copias de seguridad.

Una ventaja para este tipo de tecnologías es que pueden tener una topología de nodos, funcionan como redundancias de servicio, en cuanto se pe

![Topoligia 1](/A02.MongoDB/A02.MongoDB-Imagenes/topologia.png)

![Topoligia 2](/A02.MongoDB/A02.MongoDB-Imagenes/topologia2.png)


Nuestra base de datos en la nube requiere que configuremos
- Un usuario este se configura en la nube, sin embargo que nuestra base de datos este en la nube no indica que cualquiera se pueda conectar desde cualquier lado, en la sección de NetworkAccess tenemos declaradas las ips a las que les damos permiso de hacer el login.
    - Peering podría decirse que es una forma avanzada de utilizar las configuraciones de acceso a la red que se tengan en algun servicio de nube.

