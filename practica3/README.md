
# SWAP 2017-2018 -  Práctica 3
***Andreas Messalas, DNI: AH700994***

----------

## Preparación del sistema


Creamos 4 máquinas virtuales (en **VMware**, porque enfrentamos problemas con virtualbox):  
* **maquina1** (tiene /var/www/html/index.html que muestra "MAQUINA1")
* **maquina2** (tiene /var/www/html/index.html que muestra "MAQUINA1")
* **balanceador** (aquí está instalado nginx)
* **balanceador2** (aquí está instalado haproxy)

Los IPs de cada maquina:
* maquina1   : **192.168.44.129**
* maquina2   : **192.168.44.130**
* balanceador : **192.168.44.128**  
* balanceador2 : **192.168.44.131**

![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica3/a.png)

## 1. Instalación y configuración de Nginx en *balanceador1*

* Instalamos nginx :
 
      sudo apt-get update 
      sudo apt-get install nginx  
 * Crear un archivo de configuración en: `/etc/nginx/conf.d/default.conf`	
 (contiene los IPs de maquina1 y maquina2, que son las máquinas a las que debe repartir el tráfico)
	![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica3/b.JPG)
* Borrar el archivo de configuración predeterminado: `/etc/nginx/sites-enabled/default`
* Comenzar nginx: `sudo systemctl start nginx`
* Hacer peticiones a la dirección IP del balanceador:  `curl localhost`
( localhost: 192.168.44.28 )
![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica3/4.JPG)

El protocolo predeterminado es round robin, por lo que el balanceador sirve una máquina a la vez.

## 2. Instalación y configuración de haproxy en *balanceador2*

*  Instalamos haproxy  : `sudo apt-get install haproxy`
* Configurar el archivo de configuración en: `/etc/haproxy/haproxy.cfg`
(contiene los IPs de maquina1 y maquina2, que son las máquinas a las que debe repartir el tráfico)
![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica3/6.JPG)

* Comenzar haproxy: `sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg`
* Hacer peticiones a la dirección IP del balanceador:  `curl localhost`
( localhost: 192.168.44.28 )
![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica3/7.JPG)
El protocolo predeterminado es round robin, por lo que el balanceador sirve una máquina a la vez.

## 3. Alta carga - Apache Benchmark
Usamos la herramienta `ab` para someter a una carga muy alta  (gran número de peticiones y con alta concurrencia) a la granja web. Ejecutamos los benchmark en otra máquina diferente a las que forman parte de la granja web (servidores web o balanceador), de forma que ambos procesos no consuman recursos de la misma máquina, ya que veríamos un menor rendimiento

* Nginx
 ![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica3/c.jpg)


*Haproxy
![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica3/10.jpg)
