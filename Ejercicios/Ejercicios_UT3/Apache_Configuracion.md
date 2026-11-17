# Apache: Configuración

**Despliegue de Aplicaciones Web** · Sergio Segoviano Gálvez · 2º DAW

## Índice

1. [Configuración de Apache](#configuración-de-apache)
    1. [Instalación de Apache](#instalación-de-apache)
    2. [Creación de la página web](#creación-de-la-página-web)
    3. [Configuración del VirtualHost](#configuración-del-virtualhost)
    4. [Activación del VirtualHost](#activación-del-virtualhost)
    5. [Control de acceso](#control-de-acceso)
    6. [Problemas surgidos](#problemas-surgidos)
2. [Banco de pruebas](#banco-de-pruebas)
3. [Resultados](#resultados)
4. [Valoración personal](#valoración-personal)
5. [Bibliografía](#bibliografía)
6. [Anexos](#anexos)

## Configuración de Apache

Se han seguido los pasos 3, 4 y 5 del [tutorial oficial de Ubuntu](https://ubuntu.com/tutorials/install-and-configure-apache) en una máquina virtual con Ubuntu 24.04 LTS.

### Instalación de Apache

```bash
sudo apt update
sudo apt install apache2 -y && sudo systemctl status apache2
```

![Instalación de Apache](<capturas_configuracion/1.png>)

El servicio queda activo (`active (running)`) con la versión Apache 2.4.58.

![Estado del servicio apache2](<capturas_configuracion/salida1.png>)

### Creación de la página web

Se crea la carpeta del sitio y su `index.html`, una página sencilla con el nombre del autor, el ciclo y la localidad.

```bash
cd /var/www
sudo mkdir sergio
cd sergio
sudo nano index.html
```

![Carpeta del sitio e index.html](<capturas_configuracion/2.png>)

### Configuración del VirtualHost

Se copia el fichero por defecto y se editan sus directivas:

```bash
cd /etc/apache2/sites-available/
sudo cp 000-default.conf sergio.conf
sudo nano sergio.conf
```

![Copia de 000-default.conf](<capturas_configuracion/3.png>)

| Directiva | Valor | Función |
| --- | --- | --- |
| ServerAdmin | sergio@example.com | Correo del administrador |
| DocumentRoot | /var/www/sergio | Carpeta de la web |
| ServerName | sergio.example.com | Dominio del sitio |

![Directivas de sergio.conf](<capturas_configuracion/3salidafichero.png>)

### Activación del VirtualHost

Se activa el sitio, se comprueba la sintaxis y se recarga Apache:

```bash
sudo a2ensite sergio.conf
sudo apache2ctl configtest
sudo systemctl reload apache2
```

![Activación del sitio](<capturas_configuracion/4.png>)

Como el dominio no es real, se añade a `/etc/hosts` apuntando a `127.0.0.1`:

![Línea añadida en /etc/hosts](<capturas_configuracion/5.png>)

La web se muestra en `http://sergio.example.com`:

![Página personalizada](<capturas_configuracion/6.png>)

### Control de acceso

Se ha protegido una carpeta de la web con usuario y contraseña siguiendo el [tutorial de Geek Rewind para Ubuntu 24.04](https://geekrewind.com/how-to-set-up-apache-basic-authentication-in-ubuntu-24-04/).

**1. Instalación de las utilidades y creación del usuario.** El paquete `apache2-utils` incluye `htpasswd`, la herramienta para crear usuarios y contraseñas. La opción `-B` cifra la contraseña con bcrypt y `-c` crea el fichero de contraseñas.

```bash
sudo apt install apache2-utils -y
sudo htpasswd -Bc /etc/apache2/.htpasswd sergio
```

![Instalación de apache2-utils y creación del usuario](<capturas_configuracion/controlacceso1.png>)

Al consultar el fichero, la contraseña aparece cifrada (empieza por `$2y$`, que corresponde a bcrypt):

```bash
sudo cat /etc/apache2/.htpasswd
```

![Fichero de contraseñas cifrado](<capturas_configuracion/controlacceso1(contra).png>)

**2. Creación de la zona privada.** Se crea la carpeta `privado` con su propio `index.html`, al que solo podrán acceder los usuarios registrados.

```bash
sudo mkdir /var/www/sergio/privado
sudo nano /var/www/sergio/privado/index.html
```

![Carpeta privado](<capturas_configuracion/controlacceso2.png>)

![index.html de la zona privada](<capturas_configuracion/controlacceso2(fichero).png>)

**3. Protección en el VirtualHost.** Se añade un bloque `<Directory>` en `sergio.conf`:

```apache
<Directory /var/www/sergio/privado>
    AuthType Basic
    AuthName "Zona privada"
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
</Directory>
```

| Directiva | Función |
| --- | --- |
| AuthType Basic | Usa la autenticación básica de HTTP |
| AuthName | Texto que aparece en la ventana de login |
| AuthUserFile | Fichero con los usuarios y las contraseñas |
| Require valid-user | Solo deja pasar a los usuarios de ese fichero |

![Bloque Directory en sergio.conf](<capturas_configuracion/controlacceso3.png>)

**4. Comprobación y recarga.** Se comprueba la configuración y se recarga Apache:

```bash
sudo apache2ctl configtest
sudo systemctl reload apache2
```

![Comprobación de la configuración y recarga de Apache](<capturas_configuracion/controlacceso4.png>)

**5. Pruebas.** Al entrar en `http://sergio.example.com/privado`, el navegador pide usuario y contraseña:

![Login con el usuario sergio](<capturas_configuracion/controlacceso5.png>)

Con las credenciales correctas se muestra la zona privada:

![Zona privada](<capturas_configuracion/controlacceso5(prueba).png>)

Con un usuario que no está registrado en el fichero, Apache deniega el acceso con el error **401 Unauthorized**:

![Login con un usuario no registrado](<capturas_configuracion/usuariosincorrectoprueba.png>)

![Error 401 Unauthorized](<capturas_configuracion/usuariosincorrectoprueba1.png>)

### Problemas surgidos

| Problema | Causa | Solución |
| --- | --- | --- |
| Aparecía una página «Index of /» vacía. | Por escribir `cde sergio` en vez de `cd sergio`, el `index.html` se creó en `/var/www`. | `sudo mv /var/www/index.html /var/www/sergio/` |
| La web no cargaba desde la carpeta correcta. | `DocumentRoot` escrito como `/var/www/Sergio`; Linux distingue mayúsculas. | Corregir la ruta a `/var/www/sergio`. |
| Aviso AH00558 al comprobar la configuración. | No hay un `ServerName` global. Es solo un aviso. | Opcional: definir `ServerName localhost` con `a2enconf`. |
| `systemctl reload` fallaba. | Se ejecutó sin `sudo`. | Ejecutarlo con `sudo`. |

## Banco de pruebas

Pruebas realizadas en la máquina virtual (Ubuntu 24.04, Apache 2.4.58, Firefox), con el dominio `sergio.example.com` resuelto mediante `/etc/hosts`.

| N.º | Prueba | Resultado esperado | Resultado obtenido |
| --- | --- | --- | --- |
| 1 | `systemctl status apache2` | Servicio activo | Correcto |
| 2 | `apache2ctl configtest` | Syntax OK | Correcto |
| 3 | Acceso a `http://sergio.example.com` | Se ve la web | Correcto |
| 4 | Acceso a `http://sergio.example.com/privado` | Pide usuario y contraseña | Correcto |
| 5 | Zona privada con un usuario no registrado | Error 401 | Correcto |
| 6 | Zona privada con credenciales correctas | Se ve la zona privada | Correcto |
| 7 | Acceso de un compañero desde otro equipo | Ve la web | Pendiente |
| 8 | Compañero intenta entrar en la zona privada | Acceso denegado | Pendiente |

## Resultados

Se ha instalado Apache y se ha configurado un sitio propio con su VirtualHost, que se muestra correctamente en el navegador. Además, se ha protegido la carpeta `/privado` con autenticación básica, de forma que solo pueden acceder los usuarios registrados en el fichero de contraseñas. Los errores surgidos se debieron a fallos al escribir comandos y rutas, y se corrigieron sin dificultad.

## Valoración personal

Me ha resultado útil para recordar cosas que ya di en su momento. Lo que me ha costado han sido los errores al escribir los comandos y las rutas, que han hecho que algunos ficheros que debían crearse en una ruta acabasen en otra.

He recordado el tema del VirtualHost y cómo un mismo servidor puede alojar sin problema varios sitios. También he aprendido a proteger una carpeta con usuario y contraseña. Como mejora, añadiría HTTPS, porque la autenticación básica envía la contraseña sin cifrar.

## Bibliografía

- Canonical. *Install and Configure Apache*. https://ubuntu.com/tutorials/install-and-configure-apache (consultado el 10/11/2026).
- Geek Rewind. *How to Set Up Apache Basic Authentication in Ubuntu 24.04*. https://geekrewind.com/how-to-set-up-apache-basic-authentication-in-ubuntu-24-04/ (consultado el 10/11/2026).
- The Apache Software Foundation. *Apache HTTP Server Documentation 2.4*. https://httpd.apache.org/docs/2.4/ (consultado el 10/11/2026).

## Anexos

### Anexo A: Comandos útiles de Apache

| Comando | Para qué sirve |
| --- | --- |
| `sudo systemctl start apache2` | Arrancar Apache |
| `sudo systemctl stop apache2` | Parar Apache |
| `sudo systemctl restart apache2` | Reiniciar Apache por completo |
| `sudo systemctl reload apache2` | Aplicar cambios sin cortar las conexiones |
| `sudo a2ensite sitio.conf` | Activar un sitio |
| `sudo a2dissite sitio.conf` | Desactivar un sitio |
| `sudo a2enmod módulo` | Activar un módulo (por ejemplo, `ssl` o `rewrite`) |
| `sudo apache2ctl configtest` | Comprobar la sintaxis de la configuración |
| `sudo tail -f /var/log/apache2/error.log` | Ver los errores en tiempo real |

### Anexo B: Estructura de carpetas de Apache en Ubuntu

| Ruta | Contenido |
| --- | --- |
| `/etc/apache2/apache2.conf` | Configuración principal |
| `/etc/apache2/sites-available/` | Configuración de todos los sitios |
| `/etc/apache2/sites-enabled/` | Enlaces a los sitios activos |
| `/etc/apache2/mods-available/` y `mods-enabled/` | Módulos disponibles y activos |
| `/var/www/` | Ficheros de las webs |
| `/var/log/apache2/` | Registros de acceso (`access.log`) y de errores (`error.log`) |
