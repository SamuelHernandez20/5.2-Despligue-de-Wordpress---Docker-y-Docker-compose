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

`En este segundo bloque el servicio que defino es el del mysql:`

1. Procedo a descargar la imagen de **mysql**, en este caso una imagen de nombre **mysql:8.0**.
2. Defino las variables básicas de la **base de datos**.
3. En este caso defino un **volumen** gestionado por **docker**.
4. Reinicio.
5. Definición de red personalizada, en este caso como **backend-net**.

 ```
 mysql:
    image: mysql:8.0
    environment:
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_DATABASE=${WORDPRESS_DATABASE_NAME}
      - MYSQL_USER=${WORDPRESS_DATABASE_USER}
      - MYSQL_PASSWORD=${WORDPRESS_DATABASE_PASSWORD}
    volumes:
      - mysql_data:/var/lib/mysql
    restart: always
    networks:
      - backend-net
```
`En este tercer bloque el servicio que defino es el del phpmyadmin:`

1. Procedo a descargar la imagen de **phpmyadmin**, en este caso una imagen de nombre **phpmyadmin:5.2**.
2. Definición de puertos [máquina]:[contenedor_mysql] **8080**; **80**.
3. La parte de las variables defino el **PMA_HOST**, que era indicar el nombre del contenedor con el que quiero conectarme desde phpmyadmin, sin tener que indicarlo manualmente desde la interfaz gráfica.
4. Reinicio.
5. Definición de red personalizada, en este caso como **frontend-net** y **backend-net**.
 ```
 phpmyadmin:
    image: phpmyadmin:5.2
    ports:
      - 8080:80
    environment: 
      - PMA_HOST=mysql
    restart: always
    networks:
      - frontend-net
      - backend-net
 ```


`En este cuarto bloque el servicio que defino es el del https-portal:`

1. En esta primera línea defino el **namespace** y la imagen que voy a bajar junto a la definición de su **tag**, en este caso **1**:
2. Definición de puertos [máquina]:[contenedor_mysql] **80** y **80**; **443** y **443**.
4. Dentro de la definción de las variables de entorno primeramente defino mi **nombre de dominio** redirigido a **http://wordpress:8080**.
5. Reinicio del contenedor, incluso si se detiene por cualquier motivo de error, para garantizar su disponibilidad.
8. Definición de red personalizada, en este caso como **frontend-net**.

- **local**: Crea un certificado autofirmado para hacer pruebas en local.
- **staging**: Solicita un certificado de prueba a Let’s Encrypt para nuestro entorno de pruebas.
- **production**: Solicita un certificado válido a Let’s Encrypt. Esta opción sólo la usaremos para poner nuestro sitio web en producción.

 ```
  https-portal:
    image: steveltn/https-portal:1 # <-- al ser no verificada se le coloca el namespace.
    ports:
      - 80:80
      - 443:443
    restart: always
    environment:
      DOMAINS: '${DOMINIO} -> http://wordpress:8080' # <-- para una mejor práctica guardar en una variable el dominio, y se procede a acceder al puerto interno 8080 de wordpress definido por bitnami, y no el 8443.
      STAGE: 'production' # Don't use production until staging works
      # FORCE_RENEW: 'true'
    networks:
      - frontend-net
 ```
En esta parte de aquí se definen los volumenes de datos:

```
volumes: 
  mysql_data:
  wordpress_data:
```
Y aqui las redes definidas:

```
networks:
 frontend-net:
 backend-net:
```
