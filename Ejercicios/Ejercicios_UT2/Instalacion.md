# Despliegue de Aplicaciones Web
## Unidad 2: Iniciación a la Virtualización

**Alumno:** Sergio Segoviano Gálvez
**Curso:** 2º DAW
**Fecha:** 27/10/2026

---

## Índice

1. [Introducción](#1-introducción)
2. [Instalación de Ubuntu en VirtualBox](#2-instalación-de-ubuntu-en-virtualbox)
3. [Instalación de Docker Desktop](#3-instalación-de-docker-desktop)
4. [Contenedores de servidor web y de aplicaciones](#4-contenedores-de-servidor-web-y-de-aplicaciones)
5. [Requerimientos mínimos para implantar una aplicación web](#5-requerimientos-mínimos-para-implantar-una-aplicación-web)
6. [Conclusiones](#6-conclusiones)

---

## 1. Introducción

La **virtualización** permite ejecutar varios sistemas operativos independientes sobre una misma máquina física gracias a un **hipervisor** (en esta práctica, VirtualBox). Cada sistema virtualizado es una **máquina virtual (VM)** con su propia CPU, RAM, disco y red virtuales.

Los **contenedores** (Docker) empaquetan una aplicación con sus dependencias, pero comparten el kernel del sistema anfitrión, por lo que son más ligeros y arrancan más rápido que una máquina virtual.

| Característica | Máquina virtual | Contenedor |
|---|---|---|
| Sistema operativo | Completo (kernel propio) | Comparte el kernel del anfitrión |
| Tamaño | Varios GB | Decenas o cientos de MB |
| Arranque | Minutos | Segundos |
| Aislamiento | Muy alto | Alto (a nivel de proceso) |
| Uso típico | Simular equipos o servidores completos | Desplegar aplicaciones y servicios |

En esta práctica se combinan ambas tecnologías: una VM con Ubuntu sobre la que se instala Docker para desplegar un servidor web (**Nginx**) y un servidor de aplicaciones (**Tomcat**).

---

## 2. Instalación de Ubuntu en VirtualBox

### 2.1. Creación de la máquina virtual

Se descarga la ISO de **Ubuntu 24.04 LTS** desde la página oficial de Ubuntu (`ubuntu-24.04.1-desktop-amd64.iso`) y se crea en VirtualBox una nueva máquina virtual con la siguiente configuración:

| Parámetro | Valor |
|---|---|
| Nombre de la VM | DesplegueAplicaciones |
| Sistema operativo | Ubuntu 24.04 LTS (Noble Numbat) 64-bit |
| Usuario del sistema | ubuntu-daw2daw (equipo `ubuntu-daw2daw-VirtualBox`) |
| Memoria RAM | 4096 MB |
| Aceleración | Paginación anidada, paravirtualización KVM |
| Controlador gráfico | VMSVGA, 16 MB de vídeo |
| Disco duro virtual | DesplegueAplicaciones.vdi, 50 GB (SATA) |
| Unidad óptica | ubuntu-24.04.1-desktop-amd64.iso (IDE) |
| Red | Adaptador 1: Intel PRO/1000 MT Desktop (**NAT**) |

Con **red NAT** la VM sale a internet a través de la conexión del equipo anfitrión, sin ser visible directamente desde la red local.

![Configuración de la máquina virtual en VirtualBox](capturas/01-configuracion-vm.png)

### 2.2. Instalación del sistema

Se arranca la VM con la ISO y se sigue el asistente de instalación de Ubuntu: idioma y teclado en español, zona horaria de Madrid, instalación en el disco virtual de 50 GB y creación del usuario `ubuntu-daw2daw`. Al terminar se reinicia la máquina.

### 2.3. Instalación de las Guest Additions

Las **Guest Additions** son controladores de VirtualBox que mejoran la integración entre anfitrión e invitado: ajuste automático de la resolución, portapapeles compartido, arrastrar y soltar y carpetas compartidas.

Se instalan las herramientas necesarias para compilar los módulos del kernel:

```bash
sudo apt update
sudo apt install -y build-essential dkms linux-headers-$(uname -r) bzip2
```

Desde el menú de VirtualBox *Dispositivos → Insertar imagen de CD de las Guest Additions* se monta el CD `VBox_GAs_7.2.10` y se ejecuta el instalador para Linux (`VBoxLinuxAdditions.run`, ya que la máquina es Linux x86-64; el resto de ficheros del CD son para Windows, macOS, Solaris o ARM):

```bash
cd /media/$USER/VBox_GAs_7.2.10
sudo sh ./VBoxLinuxAdditions.run
sudo reboot
```

Para comprobar que están instaladas se consulta su versión:

```bash
VBoxClient --version
```

Resultado: `7.2.10r174163`, que coincide con la versión de VirtualBox.

![Versión de las Guest Additions](capturas/02-guest-additions-version.png)

### 2.4. Escritorio tras la instalación

![Escritorio de Ubuntu instalado](capturas/03-escritorio-ubuntu.png)

---

## 3. Instalación de Docker Desktop

Comandos ejecutados, tal como aparecen en el historial de la terminal:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install ca-certificates curl gnupg -y
sudo apt install ca-certificates curl gnupg -y
curl -fsSL https://get.docker.com | sudo sh
curl -R -O https://desktop.docker.com/linux/main/amd64/docker-desktop-amd64.deb
sudo apt-get update && sudo apt-get install ./docker-desktop-amd64.deb -y
```

![Historial de comandos de instalación de Docker](capturas/04-comandos-instalacion-docker.png)

### 3.1. Actualización del sistema

```bash
sudo apt update && sudo apt upgrade -y
```

Descarga la lista actualizada de paquetes disponibles desde los repositorios (`apt update`) y actualiza todos los paquetes instalados a su última versión (`apt upgrade`). El `&&` hace que el segundo comando solo se ejecute si el primero termina bien, y `-y` responde "sí" automáticamente.

### 3.2. Instalación de dependencias

```bash
sudo apt install ca-certificates curl gnupg -y
```

- `ca-certificates`: certificados de autoridades de confianza para las conexiones seguras HTTPS.
- `curl`: permite descargar contenido de una URL desde la terminal.
- `gnupg`: herramienta de cifrado y firmas, para verificar que los paquetes provienen realmente del repositorio y no de un tercero.

> En el primer intento se escribió `sudo install ...` sin `apt`. `install` es otro comando de Linux (copia ficheros asignando permisos), por lo que dio error y se repitió correctamente.

### 3.3. Instalación de Docker Engine

```bash
curl -fsSL https://get.docker.com | sudo sh
```

Con `curl` se descarga el script oficial de instalación de Docker y, mediante la tubería `|`, se ejecuta directamente con `sh` como administrador. El script añade el repositorio de Docker e instala Docker Engine.

- `-f`: falla sin mostrar la página de error del servidor.
- `-s` / `-S`: modo silencioso, pero mostrando los errores.
- `-L`: sigue las redirecciones.

### 3.4. Instalación de Docker Desktop

```bash
curl -R -O https://desktop.docker.com/linux/main/amd64/docker-desktop-amd64.deb
sudo apt-get update && sudo apt-get install ./docker-desktop-amd64.deb -y
```

- `-O`: guarda el instalador con su nombre original (`docker-desktop-amd64.deb`).
- `-R`: conserva la fecha de modificación del fichero remoto.
- `.deb`: formato de paquete de instalación de Debian/Ubuntu.
- `apt-get install ./fichero.deb`: refresca la lista de paquetes e instala el paquete local; el `./` indica que es un fichero de la carpeta actual, y apt instala también sus dependencias.

### 3.5. Comprobación de la instalación

```bash
docker --version
```

Resultado: `Docker version 29.9.2, build a82e528`.

![Versión de Docker instalada](capturas/05-docker-version.png)

### 3.6. Prueba con hello-world y permisos

Al ejecutar la prueba con el usuario normal aparece un error:

```text
$ docker run hello-word
permission denied while trying to connect to the docker API at unix:///var/run/docker.sock
```

![Error de permisos al ejecutar Docker](capturas/06-error-permisos.png)

Es el comportamiento esperado tras la instalación. Docker funciona con el demonio **dockerd**, que se ejecuta como **root**, y el cliente `docker` se comunica con él a través del socket `/var/run/docker.sock`. Ese socket pertenece a `root` y al grupo `docker`, y el usuario `ubuntu-daw2daw` no pertenecía a ninguno de los dos. Esto afecta a todos los comandos de Docker (`run`, `search`, `ps`…).

Hay dos formas de solucionarlo:

```bash
# Opción 1: ejecutar cada comando como administrador
sudo docker run hello-world

# Opción 2 (permanente): añadir el usuario al grupo docker
sudo usermod -aG docker $USER
newgrp docker        # o cerrar sesión y volver a entrar
```

`usermod -aG docker` añade (`-a`) el usuario al grupo (`-G`) `docker` sin quitarle los que ya tenía. A partir de ahí los comandos de Docker funcionan sin `sudo`.

> Además, en la captura el nombre de la imagen está mal escrito: es `hello-world`, no `hello-word`.

Ejecución correcta:

```bash
docker run hello-world
```

Descarga una imagen de prueba y crea un contenedor que muestra el mensaje *"Hello from Docker!"*, confirmando que Docker funciona correctamente.

![Resultado de docker run hello-world](capturas/07-hello-world.png)

---

## 4. Contenedores de servidor web y de aplicaciones

- **Servidor web**: atiende peticiones HTTP/HTTPS y sirve contenido estático. Ejemplos: Nginx, Apache HTTP Server.
- **Servidor de aplicaciones**: ejecuta la lógica de negocio de la aplicación. Ejemplos: Apache Tomcat, WildFly.

### 4.1. Búsqueda de imágenes

```bash
docker search nginx
docker search tomcat
```

`docker search` consulta Docker Hub y lista las imágenes disponibles. Se eligen las oficiales, marcadas con `[OK]` en la columna **OFFICIAL**: `nginx` y `tomcat`.

![Búsqueda de la imagen de Nginx](capturas/08-search-nginx.png)

![Búsqueda de la imagen de Tomcat](capturas/09-search-tomcat.png)

### 4.2. Creación y arranque de los contenedores

```bash
docker run -d -p 8080:80 --name webserver nginx
docker run -d -p 8081:8080 --name appserver tomcat
```

| Opción | Significado |
|---|---|
| `run` | Descarga la imagen si no existe en local (*Unable to find image... Pulling from library*) y crea y arranca el contenedor |
| `-d` | Ejecución en segundo plano (*detached*) |
| `-p 8080:80` | Redirige el puerto 8080 del anfitrión al puerto 80 del contenedor (Nginx) |
| `-p 8081:8080` | Redirige el puerto 8081 del anfitrión al 8080 del contenedor (Tomcat); se usa el 8081 porque el 8080 ya lo ocupa Nginx |
| `--name` | Nombre del contenedor (`webserver` y `appserver`) |

![Creación de los contenedores de Nginx y Tomcat](capturas/10-docker-run.png)

### 4.3. Páginas de ejemplo de Tomcat

La imagen oficial de Tomcat trae la carpeta `webapps` vacía por seguridad, de modo que al acceder desde el navegador se obtiene un **error 404** (generado por el propio Tomcat, lo que demuestra que el servidor responde). Para mostrar la página de bienvenida se copian las aplicaciones de ejemplo que vienen en `webapps.dist`:

```bash
docker exec appserver cp -r webapps.dist/. webapps/
```

- `docker exec appserver`: ejecuta un comando dentro del contenedor en marcha.
- `cp -r webapps.dist/. webapps/`: copia todo el contenido de una carpeta a la otra.

![Copia de las aplicaciones de ejemplo de Tomcat](capturas/11-docker-exec-tomcat.png)

### 4.4. Verificación de los contenedores activos

```bash
docker ps
```

Muestra los dos contenedores en ejecución con estado **Up**:

| Contenedor | Imagen | Comando | Puertos |
|---|---|---|---|
| appserver | tomcat | `catalina.sh run` | 0.0.0.0:8081 → 8080/tcp |
| webserver | nginx | `/docker-entrypoint…` | 0.0.0.0:8080 → 80/tcp |

![Contenedores activos con docker ps](capturas/12-docker-ps.png)

### 4.5. Pruebas desde el navegador

| URL | Servicio | Resultado |
|---|---|---|
| <http://localhost:8080> | Nginx | Página *"Welcome to nginx!"* |
| <http://localhost:8081> | Tomcat | Página de bienvenida de **Apache Tomcat/11.0.27** |

Ambos servidores funcionan correctamente.

![Nginx y Tomcat funcionando en el navegador](capturas/13-navegador-nginx-tomcat.png)

### 4.6. Otros comandos útiles

```bash
docker images            # lista las imágenes descargadas
docker logs appserver    # muestra el log del contenedor
docker stop webserver    # detiene un contenedor
docker start webserver   # lo vuelve a arrancar
docker rm -f webserver   # elimina un contenedor aunque esté en marcha
```

---

## 5. Requerimientos mínimos para implantar una aplicación web

### 5.1. Requisitos de hardware y software

**Hardware** (orientativo para una aplicación pequeña o mediana):

| Recurso | Mínimo | Recomendado |
|---|---|---|
| CPU | 2 núcleos | 4 núcleos o más |
| RAM | 2–4 GB | 8 GB o más (Java/Tomcat consume bastante memoria) |
| Almacenamiento | 20–50 GB | SSD, con espacio extra para logs, base de datos y copias |
| Red | Conexión estable | Ancho de banda acorde al tráfico esperado |

**Software:**

- **Sistema operativo**: preferiblemente Linux servidor (Ubuntu Server LTS, Debian, Rocky Linux) por estabilidad, seguridad y coste.
- **Servidor web**: Nginx o Apache HTTP Server.
- **Servidor de aplicaciones o intérprete**: Tomcat/WildFly (Java), PHP-FPM (PHP), Node.js…
- **Sistema gestor de base de datos**: MySQL/MariaDB, PostgreSQL…
- **Entorno de ejecución**: JDK/JRE, PHP, etc., en la versión que requiera la aplicación.
- **Herramientas de despliegue**: Docker / Docker Compose, Git y, opcionalmente, integración continua.

### 5.2. Infraestructura de red

- **Dirección IP pública** (o servidor en la nube) para que la aplicación sea accesible desde internet.
- **Nombre de dominio** y configuración **DNS** apuntando a esa IP.
- **Puertos**: 80 (HTTP) y 443 (HTTPS) abiertos al público; el resto (base de datos, puertos internos de Tomcat, administración) cerrados o accesibles solo desde la red interna.
- **Cortafuegos** (por ejemplo `ufw` en Ubuntu o los grupos de seguridad del proveedor cloud).
- **Proxy inverso**: el servidor web recibe las peticiones y las reenvía al servidor de aplicaciones, que no queda expuesto directamente.
- **Balanceador de carga** si se necesita alta disponibilidad o repartir el tráfico entre varios servidores.
- En Docker, **redes internas** entre contenedores y publicación solo de los puertos necesarios con `-p`.

Arquitectura típica:

```text
Cliente ──► Internet ──► Cortafuegos ──► Nginx (80/443) ──► Tomcat (8080) ──► Base de datos (3306)
                                         servidor web       servidor de        (red interna)
                                         / proxy inverso    aplicaciones
```

### 5.3. Configuración del servidor web y de aplicaciones

**Servidor web (Nginx):**

- Definir los *server blocks* (hosts virtuales) de cada dominio.
- Servir directamente el contenido estático, que es más eficiente que hacerlo desde la aplicación.
- Configurar el **proxy inverso** hacia el servidor de aplicaciones:

```nginx
server {
    listen 80;
    server_name miaplicacion.com;

    location / {
        proxy_pass http://appserver:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

- Activar compresión (gzip), caché de ficheros estáticos y límites de tamaño de petición.
- Configurar HTTPS y redirigir HTTP a HTTPS.

**Servidor de aplicaciones (Tomcat):**

- Desplegar la aplicación como fichero `.war` en la carpeta `webapps`.
- Ajustar la memoria de la JVM (`-Xms`, `-Xmx`) según los recursos disponibles.
- Configurar la conexión con la base de datos (pool de conexiones / *DataSource*).
- Eliminar o proteger las aplicaciones de ejemplo y el *Manager* en producción (las que se copiaron en esta práctica son solo para pruebas).

**Con Docker**, toda esta configuración puede definirse en un fichero `docker-compose.yml`, usando **volúmenes** para que los datos y la configuración no se pierdan al recrear los contenedores.

### 5.4. Seguridad y mantenimiento

**Seguridad:**

- **HTTPS** con certificados TLS (por ejemplo, gratuitos con Let's Encrypt).
- **Cortafuegos** que solo permita los puertos necesarios.
- **Principio de mínimo privilegio**: servicios con usuarios sin privilegios; controlar quién pertenece al grupo `docker`, ya que en la práctica equivale a tener permisos de administrador.
- Acceso **SSH** con clave pública y sin login directo de root.
- **Contraseñas robustas** y nunca credenciales en el código o en las imágenes (variables de entorno o *secrets*).
- Usar **imágenes oficiales** y actualizadas, y no exponer puertos internos.
- Ocultar versiones del servidor y páginas de error detalladas en producción.

**Mantenimiento:**

- **Actualizaciones** periódicas del sistema (`apt update && apt upgrade`) y de las imágenes (`docker pull`).
- **Copias de seguridad** automáticas de la base de datos, los volúmenes y la configuración, comprobando que se pueden restaurar.
- **Monitorización** de CPU, RAM, disco y disponibilidad del servicio.
- Revisión y rotación de **logs** (`docker logs`, logs de Nginx y Tomcat).
- **Limpieza** de recursos sin uso (`docker system prune`).
- **Documentación** de la instalación y de los cambios realizados.

---

## 6. Conclusiones

Se ha creado una máquina virtual con Ubuntu 24.04 LTS en VirtualBox, se han instalado las Guest Additions (versión 7.2.10) y, sobre ella, Docker (versión 29.9.2). Después se han desplegado dos contenedores, Nginx como servidor web en el puerto 8080 y Tomcat 11 como servidor de aplicaciones en el puerto 8081, comprobando su funcionamiento desde el navegador.

Durante el proceso se han resuelto problemas reales: un comando mal escrito en la instalación de dependencias, el error de permisos del socket de Docker y el error 404 inicial de Tomcat. También se ha comprobado la diferencia práctica entre ambas tecnologías: la máquina virtual requiere una instalación completa del sistema, mientras que cada contenedor se descarga y arranca en segundos con un solo comando.
