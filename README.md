### Introducción

Este documento técnico describe el proceso de configuración y despliegue de una aplicación Java en un entorno Docker utilizando WildFly como servidor de aplicaciones y PostgreSQL como base de datos. La configuración incluye la creación de Dockerfiles y un archivo docker-compose.yml para gestionar múltiples contenedores y su orquestación. Además, se implementa una capa de proxy inverso, denominada apilayer, utilizando NGINX para gestionar y asegurar el tráfico de red hacia la aplicación.

La implementación se estructura en varios servicios esenciales que interactúan entre sí para proporcionar una solución robusta y escalable. El contenedor de PostgreSQL maneja la persistencia de datos, mientras que WildFly despliega y ejecuta la aplicación Java. PgAdmin4 se incluye para la administración conveniente de la base de datos PostgreSQL. Finalmente, apilayer se configura como un proxy inverso para redirigir el tráfico entrante a los servicios correspondientes, mejorando la seguridad y la gestión del tráfico.
Este enfoque modular y contenedorizado facilita la gestión y escalabilidad de la aplicación, permitiendo un entorno de desarrollo y producción más eficiente y controlado. La guía detalla cada paso del proceso, proporcionando una comprensión clara y práctica de cómo configurar y desplegar esta arquitectura utilizando Docker.

archivo modificado **docker-compose.yml**

```
version: '3.6'

services:
  srvdb:
    image: postgres
    container_name: srvdb
    hostname: srvdb
    environment:
      POSTGRES_USER: consultas
      POSTGRES_DB: consultas
      POSTGRES_PASSWORD: QueryConSql.pwd
    ports:
      - 5432:5432
    networks:
      - demo_kcs_net

  srvwildfly:
    image: myapp
    container_name: srvwildfly
    hostname: srvwildfly
    ports:
      - 8080:8080
      - 9990:9990
    depends_on:
      - srvdb
    networks:
      - demo_kcs_net

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin
    environment:
      PGADMIN_DEFAULT_EMAIL: info@jtux.ec
      PGADMIN_DEFAULT_PASSWORD: clave
    ports:
      - 5050:80
    depends_on:
      - srvdb
    networks:
      - demo_kcs_net

  apilayer:
    image: nginx:latest
    container_name: apilayer
    hostname: apilayer
    ports:
      - "3000:80"
    depends_on:
      - srvwildfly
    networks:
      - demo_kcs_net
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf

networks:
  demo_kcs_net:

```
