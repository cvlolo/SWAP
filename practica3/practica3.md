# Práctica 3
## Nginx
  En primer lugar, vamos a configurar nginx como balanceador de carga para que se encargue de balancear las peticiones entre las dos máquinas virtuales
nuestra granja. Para ello, creamos una máquina virtual con Ubuntu Server y le instalamos nginx utilizando *apt-get install nginx*. Una vez instalado nginx,
nos vamos al archivo situado en */etc/nginx/conf.d/default.conf* e introducimos la siguiente configuración:
![img](https://github.com/cvlolo/SWAP/blob/master/practica3/ConfiguracionNginx.png)
