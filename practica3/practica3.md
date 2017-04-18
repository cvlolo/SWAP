# Práctica 3
## Nginx

En primer lugar, vamos a configurar nginx como balanceador de carga para que se encargue de balancear las peticiones entre las dos máquinas virtuales
nuestra granja. Para ello, creamos una máquina virtual con Ubuntu Server y le instalamos nginx utilizando *apt-get install nginx* y lo iniciamos utilizando *sudo systemctl start nginx*. Una vez instalado nginx,
nos vamos al archivo situado en */etc/nginx/conf.d/default.conf* e introducimos la siguiente configuración:

![img](https://github.com/cvlolo/SWAP/blob/master/practica3/ConfiguracionNginx.png)

Para comprobar que funciona, utilizamos *curl ipBalanceador* y vemos si nos devuelve el index.html de la máquina con apache instalado.

Tras comprobar que funciona, sometemos el balanceador a una alta carga utilizando el apache benchmark sobre el balanceador con un nivel de concurrencia de 200 y un total de 80000 peticiones, es decir: *ab -c 200 -n 80000 ipBalanceador*. Obteniendo los siguientes resultados:

![img](https://github.com/cvlolo/SWAP/blob/master/practica3/nginx.png)

Una vez hemos terminado con nginx, utilizamos *sudo systemctl stop nginx* para parar nginx y poder utilizar haproxy.

## Haproxy

Instalamos haproxy utilizando *apt-get install haproxy* y editamos su archivo de configuración situado en */etc/haproxy/haproxy.cfg* colocando lo siguiente:

![img](https://github.com/cvlolo/SWAP/blob/master/practica3/ConfiguracionHaproxy.png)

Una vez configurado, lanzamos el servicio con *sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg* y utilizamos de nuevo *curl ipBalanceador* para comprobar que funciona correctamente.

Volvemos a someter a una alta carga al balanceador con la misma orden que utilizamos en nginx y obtenemos los siguientes resultados:

![img](https://github.com/cvlolo/SWAP/blob/master/practica3/haproxy.png)

Para comparar ambos balanceadores nos fijamos en su tasa de transferencia, es decir, los kbytes que transfiere por segundo. Podemos ver como la tasa de transferencia de haproxy es mayor a la de nginx. Sin embargo, como estamos utilizando páginas web muy sencillas, los resultados puede que no demuestren gran cosa.

