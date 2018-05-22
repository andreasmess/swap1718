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

![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica5/4.PNG)

## **3. Restaurar dicha copia de seguridad en la segunda máquina**
