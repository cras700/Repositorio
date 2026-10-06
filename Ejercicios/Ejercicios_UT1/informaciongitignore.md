## Justificación de cada bloque
 
### 1. Contraseñas y datos sensibles
 
`.env`, `*.key`, `*.pem`, `*.p12`, `credenciales*`, `secrets*`, `config.local.php`, `config/database.php`
 
Dentro de este tipo de ficheros contienen contraseñas de bases de datos, claves de API o certificados. Si se suben al repositorio, cualquier persona con acceso a él (o todo el mundo, si es público) podría verlos y usarlos para acceder a nuestros servicios. Además, Git guarda el historial completo, por lo que aunque luego se borren seguirían visibles en commits anteriores.
 
Se hace una excepción con `!.env.example`, porque solo contiene los nombres de las variables **sin valores reales** y sirve de guía para que otros configuren su propio entorno.
 
### 2. Configuraciones locales y del IDE
 
`.vscode/`, `.idea/`, `nbproject/private/`, `*.code-workspace`, `*.local`
 
Son ajustes propios de cada desarrollador: el tema del editor, las rutas de su ordenador, las extensiones que usa, etc. No forman parte del proyecto y cada miembro del equipo tendrá los suyos. Si se subieran, al trabajar varias personas se sobrescribirían la configuración unas a otras y se generarían conflictos innecesarios.
 
### 3. Ficheros temporales y logs
 
`*.tmp`, `*.temp`, `*.bak`, `*.swp`, `*~`, `*.log`, `logs/`, `tmp/`, `temp/`, `cache/`
 
Los crean automáticamente los programas mientras trabajamos (copias de seguridad del editor, cachés, registros de errores). No aportan nada al código, cambian constantemente y ocupan espacio. Además, los logs pueden contener información sensible, como rutas del servidor o datos de usuarios.
 
### 4. Binarios y archivos compilados
 
`*.class`, `*.jar`, `*.war`, `*.exe`, `*.dll`, `*.so`, `*.o`, `build/`, `dist/`, `bin/`, `out/`
 
Se generan a partir del código fuente al compilar. Como se pueden reconstruir en cualquier momento, no tiene sentido guardarlos: el repositorio debe contener el código, no el resultado de compilarlo. Además, Git está pensado para archivos de texto; con binarios no puede mostrar las diferencias entre versiones y el repositorio crece mucho de tamaño.
 
### 5. Dependencias
 
`vendor/`, `node_modules/`
 
Son librerías de terceros descargadas con gestores como Composer o npm. Pueden ocupar cientos de megas y se reinstalan fácilmente con `composer install` o `npm install`, ya que la lista de dependencias sí se sube al repositorio (en `composer.json` o `package.json`).
 
### 6. Archivos del sistema operativo
 
`.DS_Store`, `Thumbs.db`, `desktop.ini`
 
Los crean Windows y macOS automáticamente en las carpetas para guardar miniaturas o ajustes de visualización. No tienen relación con el proyecto y solo ensucian el repositorio.
 
---
 
## Archivos subidos antes de crear el `.gitignore`
 
Si un archivo ya estaba subido antes de añadirlo al `.gitignore`, Git lo seguirá rastreando. Para dejar de hacerlo (sin borrarlo del ordenador):
 
```bash
git rm --cached nombre_archivo
git rm -r --cached carpeta/
git commit -m "Dejar de rastrear archivos ignorados"
```
 
Si ese archivo contenía una contraseña, hay que **cambiarla**, ya que sigue visible en el historial de commits anteriores.
 
Para comprobar qué regla está ignorando un archivo:
 
```bash
git check-ignore -v nombre_archivo
```
 
---
 
## Conclusión
 
El repositorio debe contener solo lo necesario para que otra persona pueda descargar el proyecto y reconstruirlo. Todo lo que es **privado, personal, temporal o regenerable** se queda fuera gracias al `.gitignore`.
