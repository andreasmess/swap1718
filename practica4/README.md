

  
# SWAP 2017-2018 -  Práctica 4
***Andreas Messalas, DNI: AH700994***

----------

## Preparación del sistema


Creamos 4 máquinas virtuales (en **VMware**, porque enfrentamos problemas con virtualbox):  
* **maquina1** (tiene /var/www/html/index.html que muestra "MAQUINA1")
* **maquina2** (tiene /var/www/html/index.html que muestra "MAQUINA1")
* **balanceador** (aquí está instalado nginx)

Los IPs de cada maquina:
* maquina1   : **192.168.44.129**
* maquina2   : **192.168.44.130**
* balanceador : **192.168.44.128**  

## **Instalar un certificado SSL autofirmado para configurar el acceso por HTTPS**

En maquina1 hacemos:

    a2enmod ssl  
    service apache2 restart  
    mkdir /etc/apache2/ssl  
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout  
    /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt
configurar el dominio:
![enter image description here](https://github.com/andreasmess/swap1718/blob/master/practica4/2.JPG?raw=true)



Editamos el archivo de configuración del sitio default-ssl  agregamos estas lineas debajo de donde pone `SSLEngine on`:
![enter image description here](https://github.com/andreasmess/swap1718/blob/master/practica4/3.JPG?raw=true)

Activamos el sitio default--ssl y reiniciamos apache:  

    a2ensite default-ssl  
    service apache2 reload
Hacemos peticiones: `curl -k https://192.168.44.129/index.html` 
![enter image description here](https://github.com/andreasmess/swap1718/blob/master/practica4/4.JPG?raw=true)

o con Chrome:
![enter image description here](https://github.com/andreasmess/swap1718/blob/master/practica4/1.JPG?raw=19true)

 Veremos el mensaje "*Your connection to this site is not secure*" que significa que el certificado es activo pero no es seguro.

Clic en "*Proceed to 192.168.44.129 (unsafe)*", veremos:
![
](https://github.com/andreasmess/swap1718/blob/master/practica4/5.JPG?raw=true)

Copiar al máquina2  y al balanceador de carga:
- De maquina2 hacemos:

  

      mkdir ./ssl
      scp maquina1@192.168.44.129:/etc/apache2/ssl/* ./ssl
      mkdir /etc/apache2/ssl
      mv ./ssl/* /etc/apache2/ssl
- De balanceador hacemos: 

    mkdir ./ssl 
    scp maquina1@192.168.44.129:/etc/apache2/ssl/* ./ssl

 
 Y despues configurar /etc/ngingx/conf.d/default.conf para aceptar y balancear correctamente tanto el tráfico HTTP como el HTTPS.
 
 ![enter image description here](https://github.com/andreasmess/swap1718/blob/master/practica4/6.JPG?raw=true)
 

## Cortafuegos con IPTABLES

Escibimos este script, *iptablesrules.sh*

   
   ![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica4/8.JPG)

Ahora veremos que de maquina2 no podemos acceser maquina1:
![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica4/9.JPG)

Antes y despues executamos iptablesrules.sh:
![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica4/7.JPG)



