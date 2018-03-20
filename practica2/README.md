
# SWAP 2017-2018 -  Práctica 2
***Andreas Messalas, DNI: AH700994***

----------
**1\. probar el funcionamiento de la copia de archivos por ssh**

- Crear 2 archivos de muestra para enviar : `sample1` , `sample2`

![](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica2/1.PNG)

- Enviar archivo en Maquina2  con ssh:
 `tar czf - ParaCopiar | ssh andreas2@192.168.1.20 'cat > ~/tmp/www.tgz'`

![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica2/2.PNG)

**2\. clonado de una carpeta entre las dos máquinas**
- Crear archivo para clonar en /var/www en Maquina 1: rsync_archivo
- En Maquina 2 :
  `rsync -avz -e ssh andreas@192.168.1.61:/var/www/rsync_archivo /var/www/`
- Finalmente  el rsync_archivo de Maquina 1 está clonado en Maquina 2
![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica2/3.PNG)


 **3\. configuración de ssh para acceder sin que solicite contraseña** 
 - Crear ssh-keygen: `ssh-keygen -b 4096 -t rsa`
![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica2/4.PNG)

- Copiar la clave a Maquina 1:  `ssh-copy-id andreas@192.168.1.61`
- Acceso con rsync sin contraseña para ssh:   
`rsync -avz -e ssh andreas@192.168.1.61:/var/www/rsync_archivo /tmp`
![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica2/5.PNG)


**4\. establecer una tarea en cron que se ejecute cada hora para mantener actualizado el contenido del directorio /var/www entre las dos máquinas**
- Cambiar /etc/crontab : 
`0 * * * * root rsync -avz -e ssh andreas@192.168.1.61:/var/www/rsync_archivo /tmp`
![enter image description here](https://raw.githubusercontent.com/andreasmess/swap1718/master/practica2/6.PNG)
