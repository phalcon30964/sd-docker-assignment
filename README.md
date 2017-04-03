# sd-docker-assignment# sd-docker-assignment

Objetivo:

En este repositorio incluya los pasos y las evidencias para el despliegue de un mirror con postgresql ó mysql y el empleo del mirror desde un contenedor de docker.

Procedimiento:

Para la realización de esta tarea, se procede a instalar un mirror  con la herramienta aptly sobre una maquina aprovisionada con vagrant. Luego se aprovisiona un contenedor de docker con postgres que consumirá los servicios del mirror.

Pruebas:

1.  Instalación de aptly: 

Se adiciona el repositorio de aplty a archivo sources.list.

* echo deb http://repo.aptly.info/ squeeze main >>  /etc/apt/sources.list

Se adiciona la key:

*sudo apt-key adv --keyserver keys.gnupg.net --recv-keys 9E3E53F19C7DE460

Se realiza un apt-get update

*apt-get update

Se instala el paquete de aptly

*apt-get install aptly


2.  Creacion del mirror en aplty:

Se genera una key RSA para crear los repositorios de aplty

*gpg --gen-key

Generamos entropia

*cat /dev/urandom

Importamos la key creada

*gpg --no-default-keyring --keyring /usr/share/keyrings/ubuntu-archive-keyring.gpg --export | gpg --no-default-keyring --keyring trustedkeys.gpg --import

Creamos un repositorio llamado xenial-main-python3, con los siguientes paquetes mysql-server, python3, postgresql, postgresql-contrib.

*aptly mirror create -architectures=amd64 -filter='Priority (required) | Priority (important) | Priority (standard) | mysql-server | python3 | postgresql | postgresql-contrib' -filter-with-deps xenial-main-python3 http://mirror.upb.edu.co/ubuntu/ xenial main

Actualizamos el mirror para hacer un cache de los paquetes a guardar.

*aptly mirror update xenial-main-python3

Creamos un snapshot del mirror para publicarlo.

*aptly snapshot create xenial-snapshot-python3 from mirror xenial-main-python3

Publicamos el mirror.

*aptly publish snapshot xenial-snapshot-python3

Exportamos la key para acceder a los repositorios del mirror.

*gpg --export --armor > my_key.pub

Corremos el servicio:

*aptly serve


3. Aprovisionamiento del contenedor docker con postgres:

Creamos un dockerfile con la siguiente configuración:

FROM ubuntu:16.04
MAINTAINER cdlopezmorcillo@gmail.com

ADD config/my_key.pub /tmp

#Configure repository
RUN apt-key add /tmp/my_key.pub 
RUN rm -f /tmp/my_key.pub 
RUN echo "deb http://192.168.131.121:8080/ xenial main" > sources.list 
RUN chmod 777 /tmp

#Install packages
RUN apt-get clean
RUN apt-get update -y 
RUN apt-get install postgresql -y
RUN apt-get install postgresql-contrib -y

#Giving permisions
EXPOSE 5432

Creamos una subinterfaz que nos permita acceder al mirror y sacar el my_key.pub. Luego hace ping al mirror

*sudo ifconfig enp5s0:0 192.168.131.129

*ping 192.168.131.121

*Sacamos la key del mirror y lo guardamos en la carpta config

A partir del archivo dockerfile creamos una imagen docker llamada centos taller_image.

*docker build -t centos_taller_image:0.0.1 . 

A partir de la imagen centos_taller_image:0.0.1 creamos un contenedor. Además mapeamos el puerto 5433 del contenedor de al 5432 del host.

*sudo docker run -it -p 5433:5432 --rm centos_taller_image:0.0.1 /bin/bash

Finalmente obtenemos el contenedor con el servidor postgres instalado. Entramos por consola y verificamos que este instalado verificando la versión instalada.

*psql –version

Luego iniciamos el servicio postgres

*/etc/init.d/postgresql restart

Entonces cambiamos de usuario a postgres

*su - postgres

Ejecutamos la consola

*psql





























Creamos en el esquema template1 , creamos una tabla distribuidos , introducimos unos registros y luego hacemos consultas de ellos:

* \c template1
* create table distribuidos (nombre varchar(100), edad int);
* insert into distribuidos (nombre,edad) values ('christian', 23)
* select * from distribuidos






