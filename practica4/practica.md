# Práctica 4
## Instalación certificado SSL

Para generar un certificado autofirmado SSL activamos el módulo SSL con *a2enmod ssl* y reiniciamos el servicio apache con *service apache2 restart*.
Ahora generamos los certificados y especificamos la ruta con *mkdir /etc/apache2/ssl* y  *openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout     /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt*.
Introducimos los datos acorde al guión y editamos el archivo de configuración que se encuentra en  */etc/apache2/sites-available/default-ssl*.
En el archivo de configuración, encontramos la línea  SSLEngine on y ponemos debajo de ella lo siguiente: *SSLCertificateFile /etc/apache2/ssl/apache.crt* y *SSLCertificateKeyFile /etc/apache2/ssl/apache.key*
Activamos el sitio default--ssl y reiniciamos apache: *a2ensite default-ssl* y *service apache2 reload*.
Probamos que está todo bien configurado entrando desde otra máquina virtual a la dirección **https://ip_Maquina**
![img](https://github.com/cvlolo/SWAP/blob/master/practica4/ssl.png)


## Configuración del cortafuegos

En esta segunda parte, vamos a configurar el cortafuegos para que deje paso al tráfico que nosotros queremos.
Para ello, vamos a crear el siguiente script:
![img](https://github.com/cvlolo/SWAP/blob/master/practica4/firewall.png)

Aquí solo dejamos paso al tráfico por los puertos 22(SSH), 80 (HTTP) y 443(HTTPS).
Una vez ejecutado el script, comprobamos que funciona correctamente ejecutando *iptables –L –n -v* para ver el estado del cortafuegos.

![img](https://github.com/cvlolo/SWAP/blob/master/practica4/puertos.png)

Una vez comprobamos que funciona correctamente, colocamos el script en crontab para que se ejecute al arrancar la máquina:

![img](https://github.com/cvlolo/SWAP/blob/master/practica4/crontab.png)

