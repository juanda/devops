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
se vuelven a desbloquear las actualizaciones e inserciones, así que en una
nueva sesión de mysql se obtienen las coordenadas:

   SHOW MASTER STATUS





CREATE USER 'replica'@'%' IDENTIFIED BY 'pruebas'; GRANT REPLICATION SLAVE ON *.* TO 'replica'@'%';

FLUSH TABLES WITH READ LOCK

UNLOCK TABLES

SHOW MASTER STATUS

START SLAVE/STOP SLAVE

CHANGE MASTER TO MASTER_HOST='12.34.56.789',MASTER_USER='slave_user', MASTER_PASSWORD='password', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=  107;



SHOW SLAVE STATUS\G
SHOW FUNCTION STATUS;
SHOW PROCEDURE STATUS;

mysqldump -uroot -p --routines --no-create-info --no-data --no-create-db --skip-opt aluni_new



En producción
-------------

- cambiar my.cnf:  

	server-id = ksksks
	log_bin = /var/log/mysql/mysql-bin.log
	binlog_format = 'MIXED'

- FLUSH TABLES WITH READ LOCK

- SHOW MASTER STATUS

- dump de ambas base de datos

- UNLOCK TABLES

Fuera de producción ya!

En slave
--------

- cambiar my.cnf:

    replicate-do-db = bd_vicalvaroSQL
	replicate-do-db = chat_aluni

- STOP SLAVE

- cargar la base de datos

- CHANGE MASTER TO MASTER_HOST='12.34.56.789',MASTER_USER='slave_user', MASTER_PASSWORD='password', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=  107;

- START SLAVE


