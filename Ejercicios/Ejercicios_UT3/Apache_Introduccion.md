# Apache: Introducción

**Despliegue de Aplicaciones Web** · Sergio Segoviano Gálvez · 2º DAW

## Resumen

Este documento recoge el trabajo realizado en el módulo de Despliegue de Aplicaciones Web de 2º de DAW sobre el servidor web Apache HTTP Server. En primer lugar se presenta el contexto en el que se desarrolla la práctica y se explica qué es Apache, para qué sirve y cómo ha evolucionado desde su nacimiento en 1995. A continuación se comparan las principales alternativas, como Nginx, LiteSpeed, Microsoft IIS o Caddy. Después se exponen los motivos por los que se ha elegido Apache como base del proyecto. Por último, se relacionan las actividades realizadas, se valoran los resultados obtenidos y se cierra con una conclusión y la bibliografía consultada.

*(Resumen provisional: se revisará al terminar el resto del documento.)*

**Palabras clave:** Apache HTTP Server, servidor web, HTTP, HTTPS, código abierto, Apache Software Foundation, Linux, Ubuntu, VirtualBox, Docker, contenedores, módulos, hosts virtuales.

## Índice

1. [Resumen](#resumen)
2. [Introducción](#introducción)
    1. [Contexto](#contexto)
    2. [¿Qué es Apache?](#qué-es-apache)
    3. [¿Para qué sirve?](#para-qué-sirve)
    4. [Historia de Apache](#historia-de-apache)
    5. [Alternativas a Apache](#alternativas-a-apache)
    6. [Motivación](#motivación)
3. [Relación de las actividades realizadas](#relación-de-las-actividades-realizadas)
    1. [Actividad 1: Creación de la máquina virtual](#actividad-1-creación-de-la-máquina-virtual)
    2. [Actividad 2: Instalación de Docker](#actividad-2-instalación-de-docker)
    3. [Actividad 3: Despliegue de un servidor web y un servidor de aplicaciones](#actividad-3-despliegue-de-un-servidor-web-y-un-servidor-de-aplicaciones)
    4. [Actividad 4: Pruebas desde el navegador](#actividad-4-pruebas-desde-el-navegador)
    5. [Actividad 5: Documentación](#actividad-5-documentación)
4. [Presentación y valoración de resultados](#presentación-y-valoración-de-resultados)
5. [Conclusión](#conclusión)
6. [Bibliografía](#bibliografía)
7. [Anexo: índice de capturas](#anexo-índice-de-capturas)

## Introducción

### Contexto

Este proyecto se realiza en el centro **[nombre del centro]**, dentro del ciclo formativo de Grado Superior de Desarrollo de Aplicaciones Web (DAW), en el segundo curso. Es un trabajo individual del módulo **Despliegue de Aplicaciones Web**, cuyo objetivo es aprender a documentar un proyecto técnico tomando como ejemplo la instalación y configuración de un servidor web.

El entorno de trabajo es una máquina virtual llamada **Ubuntu-DAW2DAW**, con **Ubuntu 24.04 LTS**, creada en **VirtualBox**. En ella se ha instalado **Docker**, que permite ejecutar servidores web y de aplicaciones en contenedores. Sobre este entorno se instalará el servidor web **Apache HTTP Server**.

### ¿Qué es Apache?

Apache HTTP Server (conocido como Apache o *httpd*) es un servidor web de código abierto y gratuito, desarrollado por la [Apache Software Foundation](https://httpd.apache.org) bajo la licencia Apache 2.0. Funciona en Linux, Windows, macOS y otros sistemas tipo Unix. La rama estable actual es la 2.4, publicada en 2012, y la última versión es la 2.4.70, de noviembre de 2026.

### ¿Para qué sirve?

Un servidor web es un programa que espera peticiones de los navegadores y les devuelve páginas, imágenes u otros recursos mediante el protocolo HTTP o HTTPS. Apache permite, entre otras cosas:

- Publicar sitios web estáticos (HTML, CSS, imágenes).
- Ejecutar aplicaciones dinámicas junto a PHP y una base de datos (la conocida pila LAMP: Linux, Apache, MySQL, PHP).
- Alojar varios sitios en un mismo servidor con los hosts virtuales.
- Cifrar las comunicaciones con HTTPS (módulo `mod_ssl`).
- Proteger zonas privadas con usuario y contraseña y configurar permisos por directorio con ficheros `.htaccess`.
- Ampliar funciones mediante módulos, como `mod_rewrite` (URL amigables) o `mod_proxy` (proxy inverso).

### Historia de Apache

- **1993-1994:** el servidor más usado era NCSA HTTPd, creado por Rob McCool en la Universidad de Illinois. Cuando McCool dejó el NCSA a mediados de 1994, su desarrollo se paró.
- **Febrero de 1995:** un grupo de webmasters, el *Apache Group*, empieza a reunir los parches y mejoras que cada uno había hecho sobre NCSA HTTPd 1.3.
- **Abril de 1995:** se publica la primera versión pública, la 0.6.2. Según la tradición, el nombre viene de «a patchy server» (un servidor a base de parches).
- **1 de diciembre de 1995:** sale Apache 1.0.
- **Abril de 1996:** Apache supera a NCSA y se convierte en el servidor web más usado de Internet, puesto que mantuvo hasta 2016.
- **1999:** los miembros del Apache Group crean la Apache Software Foundation (ASF) para dar soporte legal y económico al proyecto.
- **2012:** se publica la rama 2.4, que sigue siendo la actual.
- **2016 en adelante:** Nginx lo supera en número de sitios, aunque Apache sigue entre los servidores más usados del mundo.

### Alternativas a Apache

Según la [encuesta de Netcraft de julio de 2026](https://www.netcraft.com/blog/july-2026-web-server-survey), Apache está en tercer lugar por número de sitios web, con un 11,5 %, por detrás de Nginx (20,4 %) y Cloudflare (16,7 %). Estas son sus principales alternativas:

| Servidor | Licencia | Descripción breve |
| --- | --- | --- |
| Nginx | Código abierto (BSD) | Servidor muy rápido y ligero con muchas conexiones a la vez. Se usa mucho como proxy inverso y para contenido estático. |
| LiteSpeed | Comercial (OpenLiteSpeed es libre) | Compatible con la configuración de Apache y los `.htaccess`, pero con mejor rendimiento. Muy usado en hostings de WordPress. |
| Microsoft IIS | Propietaria (incluido en Windows Server) | Servidor web de Microsoft, pensado para aplicaciones ASP.NET en entornos Windows. |
| Caddy | Código abierto (Apache 2.0) | Servidor moderno escrito en Go que activa HTTPS automáticamente y tiene una configuración muy sencilla. |
| Apache Tomcat | Código abierto (Apache 2.0) | También de la ASF, pero orientado a aplicaciones Java (servlets y JSP), no a sitios web generales. |

### Motivación

El proyecto se realiza para aprender a montar y documentar un servidor web real, una tarea básica para cualquier desarrollador de aplicaciones web. Se ha elegido Apache por estos motivos:

- Es gratuito, de código abierto y uno de los servidores más usados del mundo, así que lo aprendido es aplicable en el trabajo.
- Tiene una documentación oficial muy completa y una comunidad grande.
- Es el servidor de la pila LAMP y de XAMPP, que usamos en clase para las prácticas de PHP y MySQL.
- Su sistema de módulos y los ficheros `.htaccess` permiten practicar de forma sencilla HTTPS, hosts virtuales y zonas privadas.

Con este proyecto se quiere conseguir:

1. Instalar Apache en la máquina virtual Ubuntu y comprobar que sirve páginas web.
2. Entender su estructura de ficheros y configuración.
3. Configurar funciones habituales, como hosts virtuales, HTTPS y acceso restringido.
4. Documentar todo el proceso siguiendo la estructura de una memoria técnica.

## Relación de las actividades realizadas

Antes de trabajar con Apache se preparó el entorno en la práctica «Iniciación a la Virtualización». Estas son las actividades realizadas, en orden:

### Actividad 1: Creación de la máquina virtual

Se descargó la ISO de Ubuntu 24.04 LTS desde la página oficial y se creó una máquina virtual en VirtualBox con estas características:

| Parámetro | Valor |
| --- | --- |
| Nombre | Ubuntu-DAW2DAW |
| Sistema operativo | Ubuntu 24.04 LTS |
| Memoria RAM | 4096 MB |
| Disco duro virtual | 50 GB |
| Tipo de red | NAT |

Después de instalar el sistema se instalaron las Guest Additions, que mejoran la integración con el equipo anfitrión (resolución de pantalla, portapapeles compartido, etc.).

### Actividad 2: Instalación de Docker

Se actualizó el sistema, se instalaron las dependencias necesarias, se instaló Docker con el script oficial y después se descargó e instaló el paquete de Docker Desktop:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install ca-certificates curl gnupg -y
curl -fsSL https://get.docker.com | sudo sh
curl -R -O https://desktop.docker.com/linux/main/amd64/docker-desktop-amd64.deb
sudo apt-get update && sudo apt-get install ./docker-desktop-amd64.deb -y
```

![Comandos de instalación de Docker](capturas/01_comandos_instalacion_docker.png)

Para comprobar la instalación se consultó la versión y se ejecutó el contenedor de prueba:

```bash
docker --version
docker run hello-world
```

![Versión de Docker](capturas/02_docker_version.png)

![Resultado de docker run hello-world](capturas/03_docker_hello_world.png)

Además, se añadió el usuario de la máquina al grupo `docker` para poder ejecutar los comandos de Docker sin usar `sudo`.

### Actividad 3: Despliegue de un servidor web y un servidor de aplicaciones

Se buscaron las imágenes disponibles y se lanzaron dos contenedores: Nginx como servidor web y Tomcat como servidor de aplicaciones.

```bash
docker search nginx
docker search tomcat
docker run -d -p 8080:80 --name webserver nginx
docker run -d -p 8081:8080 --name appserver tomcat
docker ps
```

Búsqueda de las imágenes de Nginx y Tomcat:

![docker search nginx](capturas/04_docker_search_nginx.png)

![docker search tomcat](capturas/05_docker_search_tomcat.png)

Arranque de los contenedores:

![docker run de Nginx y Tomcat](capturas/06_docker_run_contenedores.png)

Estado de los contenedores. `docker ps` muestra los que están activos en ese momento:

![Salida de docker ps](capturas/07_docker_ps.png)

### Actividad 4: Pruebas desde el navegador

Se abrieron las dos direcciones en el navegador de la máquina virtual para comprobar que los servidores respondían:

- `http://localhost:8080`: página de bienvenida de Nginx.
- `http://localhost:8081`: respuesta de Tomcat.

![Nginx y Tomcat en el navegador](capturas/08_navegador_nginx_tomcat.png)

### Actividad 5: Documentación

Se redactó un informe técnico en Markdown con el proceso, los comandos, las capturas y los requisitos mínimos para implantar una aplicación web. Se encuentra en el repositorio, dentro de *Ejercicios UT2*. Para esta memoria, además, se buscó información sobre Apache, su historia y sus alternativas.

## Presentación y valoración de resultados

El entorno quedó preparado: la máquina virtual funciona, Docker está instalado y los dos servidores de prueba respondieron correctamente.

| Prueba | Resultado |
| --- | --- |
| Arranque de Ubuntu 24.04 en VirtualBox con Guest Additions | Correcto |
| `docker run hello-world` | Correcto: muestra el mensaje de bienvenida de Docker |
| Nginx en `http://localhost:8080` | Correcto: carga la página de bienvenida |
| Tomcat en `http://localhost:8081` | Correcto: el servidor responde |

La valoración es positiva. Esta práctica ha servido para entender la diferencia entre una máquina virtual y un contenedor: la máquina virtual tiene su propio sistema operativo completo, mientras que los contenedores comparten el kernel y arrancan en segundos. También ha permitido comprobar lo rápido que es desplegar un servidor con Docker, con un solo comando.

Además, el entorno sirve como base para el siguiente paso: instalar Apache, bien directamente en Ubuntu con `apt`, bien como contenedor con la imagen oficial `httpd`. Probar Nginx antes también ayuda a entender mejor las diferencias con Apache que se describen en el apartado de alternativas.

Este es el resultado final que debe obtenerse si los pasos de Docker se han hecho bien y se han arrancado los contenedores de Nginx y Tomcat:

![Resultado final: Nginx y Tomcat funcionando](capturas/09_resultado_final.png)

## Conclusión

En esta práctica hemos repasado los pasos que hicimos de instalación de Apache HTTP Server, donde instalamos todo dentro de un contenedor, que previamente tuvimos que instalar el tema de docker para correr servicios dentro de él, en nuestro caso es el Servicio Web.

## Bibliografía

- The Apache Software Foundation. *Apache HTTP Server Project*. <https://httpd.apache.org> (consultado el 10/11/2026).
- The Apache Software Foundation. *About the Apache HTTP Server Project*. <https://httpd-mobile-responsive.staged.apache.org/ABOUT_APACHE.html> (consultado el 10/11/2026).
- The Apache Software Foundation. *ASF History Timeline*. <https://apache.org/history/timeline.html> (consultado el 10/11/2026).
- Wikipedia. *NCSA HTTPd*. <https://en.wikipedia.org/wiki/NCSA_HTTPd> (consultado el 10/11/2026).
- INAP. *Instalación, Configuración y Administración de Apache + Tomcat. Módulo 2: Historia*. <https://espaciocompartir.inap.es/v3/pluginfile.php/3658/mod_resource/content/16/Historia.pdf> (consultado el 10/11/2026).
- Netcraft. *July 2026 Web Server Survey*. <https://www.netcraft.com/blog/july-2026-web-server-survey> (consultado el 10/11/2026).
- Linux Compatible. *Apache HTTP Server 2.4.70 Released*. <https://www.linuxcompatible.org/story/apache-http-server-2470-released-bugfix-update-supersedes-all-earlier-24x-versions/> (consultado el 10/11/2026).

## Anexo: índice de capturas

Todas las capturas están en la carpeta `capturas/`, junto a este fichero.

| N.º | Fichero | Apartado | Qué muestra |
| --- | --- | --- | --- |
| 1 | `01_comandos_instalacion_docker.png` | Actividad 2 | Comandos de instalación de Docker y Docker Desktop |
| 2 | `02_docker_version.png` | Actividad 2 | `docker --version` (Docker 29.9.2) |
| 3 | `03_docker_hello_world.png` | Actividad 2 | Resultado de `docker run hello-world` |
| 4 | `04_docker_search_nginx.png` | Actividad 3 | `docker search nginx` |
| 5 | `05_docker_search_tomcat.png` | Actividad 3 | `docker search tomcat` |
| 6 | `06_docker_run_contenedores.png` | Actividad 3 | Arranque de los contenedores de Nginx y Tomcat |
| 7 | `07_docker_ps.png` | Actividad 3 | Contenedores activos con `docker ps` |
| 8 | `08_navegador_nginx_tomcat.png` | Actividad 4 | Nginx (8080) y Tomcat (8081) en el navegador |
| 9 | `09_resultado_final.png` | Resultados | Resultado final con los dos servidores funcionando |
