# Práctica 6
## Configuración del RAID por software

Lo primero que haremos será ir a la configuración de la máquina virtual. En la parte de almacenamiento, añadiremos dos discos duros,
quedando la máquina virtual de la siguiente forma:

![img](https://github.com/cvlolo/SWAP/blob/master/practica6/raidcap01.png)

Buscamos la identificación de los discos utilizando *sudo fdisk -l* y nos aparece lo siguiente:

![img](https://github.com/cvlolo/SWAP/blob/master/practica6/raidcap1.png)

Ahora ya podemos creamos el RAID 1, usando el dispositivo /dev/md0, indicando el número de dispositivos a utilizar ysu ubicación: 
*sudo mdadm -C /dev/md0 --level=raid1 --raid-devices=2 /dev/sdb /dev/sdc*

Una vez creado el dispositivo RAID usamos /dev/md0 para darle formato: *sudo mkfs /dev/md0*

Creamos el directorio sobre el que se montará la unidad del RAID: 
*sudo mkdir /dat sudo mount /dev/md0 /dat *
 Podemos comprobar que el proceso se ha realizado adecuadamente y comprobar que el RAID se hizo sastisfactoriamente, ejecutaremos: 
 *sudo mdadm --detail /dev/md0* y obtendremos lo siguiente:
 
 ![img](https://github.com/cvlolo/SWAP/blob/master/practica6/raidcap2.png)

 Para que el RAID se monte con cada arranque del sistema, hay que añadirlo al archivo de configuración */etc/fstab*. Para ello ejecutamos
 *ls -l /dev/disk/by-uuid/* y anotamos el UUID de nuestro RAID además de añadirlo al fstab de la siguiente forma:
 
  ![img](https://github.com/cvlolo/SWAP/blob/master/practica6/raidcap3.png)
  
  Una vez acabado, simulamos un fallo en uno de los discos con *sudo mdadm --manage --set-faulty /dev/md0 /dev/sdb* 
  Se aprecia como hay 1 dispositivo con fallos.
 ![img](https://github.com/cvlolo/SWAP/blob/master/practica6/raidcap4.png)

  Ahora eliminamos ese mismo disco con fallos utilizando *sudo mdadm --manage --remove /dev/md0 /dev/sdb*
  
  ![img](https://github.com/cvlolo/SWAP/blob/master/practica6/raidcap5.png)
  
  Y por último, podemos añadir, en caliente, un nuevo disco que vendría a reemplazar al disco que hemos retirado: *sudo mdadm --manage --add /dev/md0 /dev/sdb*
    ![img](https://github.com/cvlolo/SWAP/blob/master/practica6/raidcap6.png)
    ![img](https://github.com/cvlolo/SWAP/blob/master/practica6/raidcap67png.png)


  


 
 
