# 5.2-Despligue-de-Wordpress---Docker-y-Docker-compose

En esta primera práctica de ejemplo se busca desplegar mediante un archivo **.yml** de **docker-compose** desplegar un sitio web de **Wordpress** haciendo uso de **HTTPS-PORTAL** que es una imagen Docker que contiene un servidor HTTPS totalmente automatizado que hace uso de las tecnologías **Nginx** y **Let’s Encrypt**. Los certificados **SSL** se obtienen y renuevan de **Let’s Encrypt automáticamente**.

 # Estructura de Directorios:
 
```
    │   ├── .env
    │   ├── docker-compose.yml

```
## 1. Archivo de variables:

 Dentro de el archivo **.env** estás serán las variables que hará falta:
```
WORDPRESS_DATABASE_PASSWORD=bitnami 
WORDPRESS_BLOG_NAME=SITIO_SAMUEL
WORDPRESS_DATABASE_HOST=mysql
WORDPRESS_DATABASE_USER=bn_wordpress
WORDPRESS_DATABASE_NAME=bitnami_wordpress
WORDPRESS_USERNAME=Samuel
WORDPRESS_PASSWORD=wp_password
WORDPRESS_EMAIL=samuel@example.com

MYSQL_ROOT_PASSWORD=root
DOMINIO=wordpressamuel.ddns.net
```
## 2. Explicación del archivo docker-compose.yml:

En la primera línea nos encontramos con el establecimiento de la **versión**, útil por si hay **incompatibilidades** con los servicios:

```
version: '3'
```
`En este primer bloque el servicio que se define es el de mysql:`


