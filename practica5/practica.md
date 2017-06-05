# Práctica 5
## Crear una BD e insertar datos

Para crear una base de datos lo primero que tenemos que hacer es entrar en mysql con mysql -u root -p

y creamos una base de datos con los siguientes comandos, dentro de mysql: mysql> create database contactos; 
mysql> use contactos; mysql> create table datos(nombre varchar(100),tlf int); mysql> show tables;

+---------------------+

| Tables_in_contactos |

+---------------------+

| datos |

+---------------------+

1 row in set (0,00 sec)

mysql> insert into datos(nombre,tlf) values ("pepe",95834987);

mysql> select * from datos;

+---------+-----------+

| nombre | tlf |

+---------+-----------+

| pepe | 95834987 |

+---------+-----------+

3 rows in set (0,00 sec)
Hacer una consulta

Para hacer una consulta ejecutaremos los siguientes comandos dentro de mysql:

mysql> use contactos;

mysql> select * from datos;

mysql> describe datos;

## Replicar una BD MySQL con mysqldump

MySQL ofrece la una herramienta para clonar las BD que tenemos en nuestra maquina. Esta herramienta es mysqldump. Lo primero que tenemos que hacer es entrar en mysql y ejecutar lo siguiente : mysql> FLUSH TABLES WITH READ LOCK; para evitar que se acceda a la base de datos. Ahora ya sí podemos hacer el mysqldump para guardar los datos. En el servidor principal (maquina1) hacemos: mysqldump ejemplodb -u root -p > /tmp/ejemplodb.sql Como habíamos bloqueado las tablas, debemos desbloquearlas (quitar el “LOCK”): mysql> UNLOCK TABLES; Ya podemos ir a la máquina esclavo (maquina2, secundaria) para copiar el archivo .SQL con todos los datos salvados desde la máquina principal (maquina1): scp maquina1:/tmp/ejemplodb.sql /tmp/. Ahora nos vamos a la máquina 2 y ejecutamos lo siguiente: mysql> CREATE DATABASE ‘ejemplodb’; y mysql -u root -p ejemplodb < /tmp/ejemplodb.sql
Replicación de BD mediante una configuración maestro-esclavo

Para automatizar todo el proceso anterior debemos hacer una configuración maestro-esclavo. Para ello nos vamos a la máquina 1 (maestro) y editamos el fichero /etc/mysql/mysql.conf.d/mysqld.cnf, comentamos el siguiente parámetro #bind-address 127.0.0.1, descomentamos la siguiente línea log_error = /var/log/mysql/error.log, descomentamos el server-id = 1 y por último descomentamos log_bin = /var/log/mysql/bin.log. Guardamos el documento y reiniciamos el servicio: /etc/init.d/mysql restart, una vez hecho estó configuramos al esclavo que sería exactamente igual pero con server-id=2. Cuando tengamos todo esto vamos al maestro y entramos en mysql y ejecutamos lo siguiente: mysql> CREATE USER esclavo IDENTIFIED BY 'esclavo';

mysql> GRANT REPLICATION SLAVE ON . TO 'esclavo'@'%' IDENTIFIED BY 'esclavo';

mysql> FLUSH TABLES;

mysql> FLUSH TABLES WITH READ LOCK;

Para finalizar con la configuración en el maestro, obtenemos los datos de la base de datos que vamos a replicar para posteriormente usarlos en la configuración del esclavo: mysql> SHOW MASTER STATUS;

![img](https://github.com/cvlolo/SWAP/blob/master/practica5/master.png)


volvemos a la máquina esclavo a mysql y ejecutamos lo siguiente:

mysql> CHANGE MASTER TO MASTER_HOST='192.168.31.100', MASTER_USER='esclavo', MASTER_PASSWORD='esclavo', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=501, MASTER_PORT=3306; donde MASTER_LOG_FILE colocamos lo que viene en FILE MASTER_LOG_POS lo que viene en position y la ip la del maestro.

Ahora arrancamos al esclavo en mysql con : mysql> START SLAVE; Por último, volvemos al maestro y volvemos a activar las tablas para que puedan meterse nuevos datos en el maestro: mysql> UNLOCK TABLES; Ahora, si queremos asegurarnos de que todo funciona perfectamente y que el esclavo no tiene ningún problema para replicar la información, nos vamos al esclavo y con la siguiente orden: mysql> SHOW SLAVE STATUS\G revisamos si el valor de la variable “Seconds_Behind_Master” es diferente a null como en la siguiente captura(en nuestro caso es 0): 

![img](https://github.com/cvlolo/SWAP/blob/master/practica5/master2.png)


Para comprobar que todo funciona, debemos ir al maestro e introducir nuevos datos a la base de datos. A continuación vamos al esclavo para comprobar que los cambios se han realizado:

![img](https://github.com/cvlolo/SWAP/blob/master/practica5/master3.png)


