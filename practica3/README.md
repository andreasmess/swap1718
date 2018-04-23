
# SWAP 2017-2018 -  Práctica 3
***Andreas Messalas, DNI: AH700994***

----------
**Preparación del sistema**

Creamos 4 máquinas virtuales (en **VMware**, porque enfrentamos problemas con virtualbox):  
* Maquina1 (tiene /var/www/html/index.html que muestra "MAQUINA1")
* Maquina2 (tiene /var/www/html/index.html que muestra "MAQUINA1")
* Balanceador (aquí está instalado nginx)
* Balanceador2 (aquí está instalado nginx)

Los IPs cada maquina:
* Maquina1   : 192.168.44.129
* Maquina2   : 192.168.44.130
* Balanceador : 192.168.44.128  
* Balanceador2 : 192.168.44.131



