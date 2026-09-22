# GitHub: Funciones Principales (1)
 
_GitHub_ es la plataforma que permite alojar proyectos usando **Git** como sistema de control de versiones.
 
## ¿Qué es un [repositorio](https://docs.github.com/es/repositories)? (2)
 
Un repositorio es el espacio de GitHub donde se guarda todo el código, la documentación y el historial de cambios de un proyecto.
 
> Un repositorio puede ser público o privado, y admite colaboradores que trabajen sobre él.
 
Dentro de un repositorio se puede crear un **README.md** con toda la documentación del proyecto, siguiendo la [guía oficial](https://docs.github.com/es/repositories).
 
> Cada cambio subido a un repositorio queda registrado como un commit, con su autor, fecha, mensaje descriptivo y los archivos modificados. Esto permite recuperar cualquier versión anterior del proyecto en caso de error o pérdida de código, algo fundamental cuando se trabaja en equipo sobre los mismos archivos.
 
Para consultar el historial completo se puede acceder desde la pestaña de [commits](https://docs.github.com) del repositorio.
 
###### Comandos básicos de Git (3)
 
Antes de trabajar desde la web, es habitual clonar el repositorio en local.
 
```
git clone https://github.com/cras700/pruebaGithub.git
git add .
git commit -m "mensaje descriptivo"
git push origin main
```
 
Estos comandos son el equivalente en terminal de subir archivos y hacer commit desde la web, y son especialmente útiles cuando se trabaja con **archivos grandes** o de forma habitual.
 
![GitHub Mark](https://libraries.mit.edu/app/uploads/sites/4/2017/08/GitHub-Mark.png)
 
Una vez subidos los cambios con `git push`, quedan reflejados en la rama indicada, normalmente **main**.
 
## Ramas y colaboradores (2)
 
Para trabajar sin afectar directamente a la rama principal se crea una nueva rama, y los cambios se integran después mediante una [pull request](https://docs.github.com/es/pull-requests).
 
| Paso | Acción | Resultado |
| ------------- |:-------------:|-------------|
| 1 | Crear rama | Copia paralela de `main` |
| 2 | Editar archivos | Cambios aislados en la rama |
| 3 | Pull request | Revisión antes de fusionar |
| 4 | Merge | Cambios integrados en `main` |
 
### Funciones exploradas en la práctica
 
* Crear repositorio y README
* Subir archivos y hacer commit
* Revisar historial de commits
* Crear rama y pull request
* Añadir colaboradores
### Pasos para añadir un colaborador
 
1. Entrar en **Settings** del repositorio.
2. Ir al apartado **Collaborators**.
3. Pulsar **Add people** y buscar el usuario.
