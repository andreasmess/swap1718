# SWAP 2017-2018 -  Práctica 5
***Andreas Messalas, DNI: AH700994***

----------

## Preparación del sistema


Creamos 3 máquinas virtuales (en **VMware**, porque enfrentamos problemas con virtualbox):  
* **maquina1** (tiene /var/www/html/index.html que muestra "MAQUINA1")
* **maquina2** (tiene /var/www/html/index.html que muestra "MAQUINA1")
* **balanceador** (aquí está instalado nginx)

Los IPs de cada maquina:
* maquina1   : **192.168.44.129**
* maquina2   : **192.168.44.130**
* balanceador : **192.168.44.128**  

## **Instalar MySQL**

    sudo apt-get update
    sudo apt-get install mysql-server
    mysql_secure_installation

## **1. Crear la Base de Datos**
Al primer entramos en este sistema. Luego creamos una BD con nombre "MiDB". Despues creamos una tabla que se llama "datos". Ejecutamos los siguientes comandos:
mysql -u root -p

    CREATE DATABASE MiDB;
    USE MiDB;
    CREATE TABLE datos(nombre varchar(100), tlf int);

![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica5/1.PNG)

Insertamos datos en la tabla:
![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica5/2.PNG)

Mostramos el contenido de datos:
![enter image description here](https://github.com/andreasmess/swap1718/blob/master/3.PNG?raw=true)

## **2. Copia de seguridad de la BD**

Realizar la copia de seguridad de la base de datos

    mysqldump MiDB -u root -p  > /root/MiDB.sql

El maquina1 copiamos el archivo MiDB.sql a /home/maquina1/ : 

    sudo /root/MiDB.sql /home/maquina1/

Luego copiamos el archivo de copia de seguridad a la máquina  secundaria. 

    scp  maquina1@192.168.44.129:/home/maquina1/MiDB.sql  /root/



## **3. Restaurar dicha copia de seguridad en la segunda máquina**
Installamos la base de datos copiada en maquina2, después de que hemos creado una base de datos con nombre "*copyMiDB*"

    mysql -u root -p copyMiDB < /root/MiDB.sql

 ![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica5/5.png)

Al final vemos que la copyMiDB funciona, como en maquina1.
![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/6.png)

## 4. Configuración maestro-esclavo de los servidores MySQL

Configuramos el archivo [/etc/mysql/mysql.conf.d/mysqld.cnf](https://github.com/andreasmess/swap1718/blob/master/practica5/mysqld.cnf)

Reiniciamos el servicio con el comando `/etc/init.d/mysql restart`
![enter image description here](https://github.com/andreasmess/swap1718/blob/master/practica5/7.PNG?raw=true)

Crearemos un usuario para la máquina esclava
![enter image description here](https://github.com/andreasmess/swap1718/blob/master/practica5/8.PNG?raw=true)

Configuramos maquina2 con la misma manera y reiniciamos el servicio con el comando `/etc/init.d/mysql restart`

Indicamos quién es el maestro en la máquina esclava con este comando:

    CHANGE MASTER TO MASTER_HOST='192.168.44.129', MASTER_USER='slave', MASTER_PASSWORD='slave', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=501, MASTER_PORT=3306;

Asegurarnos de que todo funciona perfectamente

    mysql> SHOW SLAVE STATUS\G;
    *************************** 1. row ***************************
                   Slave_IO_State:
                      Master_Host: 192.168.44.129
                      Master_User: slave
                      Master_Port: 3306
                    Connect_Retry: 60
                  Master_Log_File: mysql-bin.000001
              Read_Master_Log_Pos: 501
                   Relay_Log_File: ubuntu-relay-bin.000002
                    Relay_Log_Pos: 4
            Relay_Master_Log_File:
                 Slave_IO_Running: No
                Slave_SQL_Running: Yes
                  Replicate_Do_DB:
              Replicate_Ignore_DB:
               Replicate_Do_Table:
           Replicate_Ignore_Table:
          Replicate_Wild_Do_Table:
      Replicate_Wild_Ignore_Table:
                       Last_Errno: 0
                       Last_Error:
                     Skip_Counter: 0
              Exec_Master_Log_Pos: 0
                  Relay_Log_Space: 154
                  Until_Condition: None
                   Until_Log_File:
                    Until_Log_Pos: 0
               Master_SSL_Allowed: No
               Master_SSL_CA_File:
               Master_SSL_CA_Path:
                  Master_SSL_Cert:
                Master_SSL_Cipher:
                   Master_SSL_Key:
            Seconds_Behind_Master: 0
    Master_SSL_Verify_Server_Cert: No
                    Last_IO_Errno: 1236
                    Last_IO_Error: Got fatal error 1236 from master when reading data from binary log: 'Client requested master to start replication from position > file size'
                   Last_SQL_Errno: 0
                   Last_SQL_Error:
      Replicate_Ignore_Server_Ids:
                 Master_Server_Id: 1
                      Master_UUID: 37db6286-5de5-11e8-b66c-000c29823a49
                 Master_Info_File: /var/lib/mysql/master.info
                        SQL_Delay: 0
              SQL_Remaining_Delay: NULL
          Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
               Master_Retry_Count: 86400
                      Master_Bind:
          Last_IO_Error_Timestamp: 180523 09:35:11
         Last_SQL_Error_Timestamp:
                   Master_SSL_Crl:
               Master_SSL_Crlpath:
               Retrieved_Gtid_Set:
                Executed_Gtid_Set:
                    Auto_Position: 0
             Replicate_Rewrite_DB:
                     Channel_Name:
               Master_TLS_Version:
    1 row in set (0.00 sec)
    
    ERROR:
    No query specified
    
    mysql>

![enter image description here](https://github.com/andreasmess/swap1718/blob/master/practica5/9.PNG?raw=true)

![enter image description here](https://github.com/andreasmess/swap1718/blob/master/practica5/10.PNG?raw=true)
