Conceptos Generales
-------------------

En el master hay que habilitar los binary logs y definir un número único
de servidor.

En cada slave hay que definir un número único de servidor.

Crear un usuario en el master para replicación (OPCIONAL).

Antes de crear un snapshot del master hay que recoger la posición del
binary log en el master. Esta información hay que dársela al slave
para que sepa desde donde tiene que leer los binary log para sincronizar.

El snapshot se puede hacer mediante un volcado con mysqldump o copiando
directamente el archivo de la base de datos maestra.

Hay que configurar al slave con los datos del usuario de replicación
y con los datos del master (host, user, password, coordenadas binary log).


Fuente: http://dev.mysql.com/doc/refman/5.5/en/replication-howto.html

----

La versión "major" del slave debe ser superior en uno a la del slave.

Fuente: http://dev.mysql.com/doc/refman/5.5/en/replication-compatibility.html

----

Configurando replicación con datos existentes
---------------------------------------------

1. Crear un usuario de replicación en el master.

CREATE USER 'repl'@'%.mydomain.com' IDENTIFIED BY 'slavepass';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%.mydomain.com';

2. Configurar master (server-id y log-bin). 

3. Obtener las coordenadas actuales del log-bin. Hay que parar las
inserciones y actualizaciones, obtener las coordenadas y hacer
snapshot antes de volver a permitirlas de nuevo.

   FLUSH TABLES WITH READ LOCK

cuidado que si salimos del cliente en el que hemos lanzado la instrucción
se vuelven a desbloquear las actualizaciones e insercione, así que en una
nueva sesión de mysql se obtienen las coordenadas:

   SHOW MASTER STATUS
