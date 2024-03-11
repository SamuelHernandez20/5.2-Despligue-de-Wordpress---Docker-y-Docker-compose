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
`En este primer bloque el servicio que se defino es el del wordpress:`

1. Procedo a descargar la imagen de prestashop, en este caso una imagen de nombre **bitnami/wordpress:6.4.3**.
2. Defino las variables de **wordpress**, la primera como en principio no está destinada a cambiar la escribo directamente.
3. Defino las sucesivas variables de la base de datos, etc...
4. En cuanto a los volumenes será uno gestionado por docker.
5. Dependerá del servicio de **mysql**.
6. Definición de red personalizada, en este caso como **frontend-net** y **backend-net**.
```
services:
  wordpress:
    image: bitnami/wordpress:6.4.3 # <-- bajarlo de bitnami para agregarle mas variables de conf al wordpress. 
    
    environment: 
      - WORDPRESS_DATABASE_HOST=mysql
      - WORDPRESS_DATABASE_NAME=${WORDPRESS_DATABASE_NAME}
      - WORDPRESS_DATABASE_USER=${WORDPRESS_DATABASE_USER}
      - WORDPRESS_DATABASE_PASSWORD=${WORDPRESS_DATABASE_PASSWORD}
      - WORDPRESS_BLOG_NAME=${WORDPRESS_BLOG_NAME}
      - WORDPRESS_USERNAME=${WORDPRESS_USERNAME}
      - WORDPRESS_PASSWORD=${WORDPRESS_PASSWORD}
      - WORDPRESS_EMAIL=${WORDPRESS_EMAIL}

    volumes: 
      - wordpress_data:/bitnami/wordpress
    depends_on:
      - mysql
    restart: always
    networks:
      - frontend-net
      - backend-net
```
