# -*- mode: org -*-
# -*- coding: utf-8 -*-
#+EXPORT_FILE_NAME: index.html
#+SETUPFILE: https://fniessen.github.io/org-html-themes/setup/theme-readtheorg.setup
#+AUTHOR: Marcos Martínez Pérez
#+EMAIL: mmperez@esei.uvigo.es
#+TITLE: Git
#+DATE: <2020-12-17>
#+language:  es

En el siguiente documento aprenderemos cosas sobre:

- git
- gestión de proyectos
- crear copias de proyectos
- adaptar proyectos
- participar en proyectos.

Puedes trabajar con git desde herramientas gráficas o desde la línea de comandos.

En este caso vamos a trabajar con la línea de comandos, preferiblemente en GNU/Linux.

* Instalación de git
En GNU/Linux, tan solo hay que instalar =git-core=:
#+BEGIN_SRC sh
apt-get install git-core
#+END_SRC

En Mac se puede instalar el [[https://sourceforge.net/projects/git-osx-installer/][instalador gráfico de git]] y en Windows, [[https://git-for-windows.github.io/][git for Windows]].

#+BEGIN_QUOTE
Para usar git desde la terminal en Mac hay que activar las funciones de Xcode.
#+END_QUOTE

Si queremos disfrutar de una terminal potente, podríamos usar [[http://cygwin.com][Cygwin]] en Windows o la Terminal en  Mac.

Comprobamos que se ha instalado git con la opción =--version=

#+BEGIN_SRC sh
git --version
#+END_SRC

También tenemos los instaladores oficiales de [[http://www.git-scm.com][git]]:
- Mac, http://www.git-scm.com/download/mac
- Windows, http://www.git-scm.com/download/win

* Llave SSH

#+BEGIN_QUOTE
Si no sabes qué es SSH, sáltate esto
#+END_QUOTE

Las claves SSH son una forma de identificar ordenadores de confianza sin comprometer contraseñas. Se peude generar unas claves SSH y añadir la clave pública de GitHub para que se produzca la conexión.

GitHub recomienda revisar regularmente la lista de claves SSH y revocar aquellas que no se usen, no se hayan usado o no se vayan a usar.

Puedes conectarte por ssh y activar la llave ssh para conectarte de forma autentificada automáticamente. Vayamos paso a paso.

** Comprobación de claves

Primero comprobamos que contamos con clave ssh en el equipo:

#+BEGIN_SRC sh
ls -la ~/.ssh
#+END_SRC

Si aparece un listado de claves, podremos saltarnos el siguiente paso. Si no, debemos generar unas claves.

** Generar claves ssh

Necesitas generar una clave ssh el equipo local desde el que te conectas:

#+BEGIN_SRC sh
ssh-keygen -t rsa -b 4096 -C "correo-electronico@dominio.com"
#+END_SRC

Lo cual crea una nueva clave ssh y utiliza el correo electrónico como etiqueta.

Si todo va bien, mostrará el mensaje de generación de la clave, pedirá dónde almacenarla y se puede añadir una contraseña:

#+BEGIN_EXAMPLE
Generating public/private rsa key pair.
Enter file in which to save the key (/home/usuarix/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/usuarix/.ssh/id_rsa.
Your public key has been saved in /home/usuarix/.ssh/id_rsa.pub.
The key fingerprint is:
(...)
#+END_EXAMPLE

=(...)= es donde aparece la clave.

Ahora que ya tenemos la clave, la pegamos en GitHub en las preferencias, en el apartado "SSH and GPG keys".

*** Copia de clave con método =pbcopy=
Para seleccionar la clave, podemos emplear el método MacOSX =pbcopy=, que podemos hackear en GNU/Linux con un /alias/ a partir de =xsel=:

#+BEGIN_SRC sh
alias pbcopy='xsel --clipboard --input'
alias pbpaste='xsel --clipboard --output'
#+END_SRC

De esta forma ya podemos utilizar =pbcopy=:

#+BEGIN_SRC sh
pbcopy < ~/.ssh/id_rsa.pub
#+END_SRC

Y pegamos en GitHub. A partir de ahí ya podremos conectarnos con GitHub de forma segura.

*** Copia de clave con =more= y copiar y pegar

Podemos hacerlo en dos pasos, mostrando la clave y copiándola con el ratón:

#+BEGIN_SRC sh
more ~/.ssh/id_rsa.pub
#+END_SRC

** Configuración local y comprobación

Ya está casi todo hecho. Ahora falta decirle a git que nos conectamos a GitHub de forma segura. Para ello, podemos comprobar que lo podemos hacer, y en el mismo paso aprobar la conexión:

#+BEGIN_SRC sh
ssh -T git@github.com
#+END_SRC

Nos pedirá la contraseña que hayamos puesto a la clave si lo hemos hecho, lo introducimos y listo. Si no, nos saldrá directamente el mensaje:

#+BEGIN_EXAMPLE
The authenticity of host 'github.com (192.30.252.1)' can't be established.
RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
Are you sure you want to continue connecting (yes/no)?
#+END_EXAMPLE

Nótese que =192.30.252.1= es una de las direcciones IP de GitHub, pero podría salir otra. Lo más importante es fijarse en el fingerprint.

Le decimos que sí y entonces GitHub nos responde:

#+BEGIN_EXAMPLE
Hi usuarix! You've successfully authenticated, but GitHub does not provide shell access.
#+END_EXAMPLE

Donde =usuarix= es nuestrx usuarix en GitHub. Ya está hecho.

Si nos apareciese el mensaje =access denied=, recomiendo seguir los pasos anteriores o [[https://help.github.com/articles/error-permission-denied-publickey][este artículo de GitHub]] para comprobar que lo hemos hecho bien.

* Cambiar el editor por defecto
Por defecto, bash y git vienen con el editor =vi= por defecto. Para cambiarlo, tal como explican en [[https://stackoverflow.com/questions/2596805/how-do-i-make-git-use-the-editor-of-my-choice-for-commits][stackoverflow]], podemos hacerlo en una o en
ambas.
** core.editor
Para usar =nano= o el editor de texto CLI de nuestra elección, corremos:
#+BEGIN_EXAMPLE
git config --global core.editor "nano"
#+END_EXAMPLE

La opción =--global= es para hacerlo en todo git. Si solo quisiéramos en este repositorio, sería sin esa
opción.
** nano como editor por defecto
Lo hacemos en dos líneas, con dos variables de entorno:

#+BEGIN_EXAMPLE
export VISUAL=nano
export EDITOR=$VISUAL
#+END_EXAMPLE
* Configuración

La primera vez que usas Git te pedirá tu nombre de usuarix y dirección de correo. Lo podemos agregar con el comando =config=.

Tal como comenta [[https://stackoverflow.com/a/16682441][sarigalin]] hay tres niveles de configuración de =git=, de más específico a más general, siendo
la más específica la que sobreescribe la más general.

- =project=, configuración de proyecto, solo disponible para el actual proyecto. Se almacena en =.git/config=
  en el directorio del proyecto.
- =global=, configuración global para todos los proyectos del usuario del sistema. Se almacena en =~/.gitconfig=.
- =system=, configuración para el sistema, para todas las cuentas del sistema.

Por tanto, si queremos tener una configuración para el sistema, escribiremos:

#+begin_example
git config --system user.name "Fulanito de Tal"
#+end_example

Si queremos que sea global para nuestra cuenta:

#+begin_example
git config --global user.name "Fulanito de Tal"
#+end_example

Y si queremos que sea especifica del repositorio, que es la configuración por defecto, no hay que añadir
=--project= como en los otros dos casos.

#+begin_example
git config  user.name "Fulanito de Tal"
#+end_example

** Configuración global

Añado el nombre de la cuenta, en este caso el nombre de usuarix en GitHub:

#+BEGIN_SRC sh
git config --global user.name "Nombre_de_Usuarix"

#+END_SRC
Añado la dirección de correo electrónico:
#+BEGIN_SRC sh
git config --global user.email "usuarix@dominio"
#+END_SRC

Si no queremos aplicar esta configuración a todo el sistema y solo a este repositorio porque manejamos más usuarixs de GitHub, por ejemplo, no pongáis la opción =--global=

Cuando hagamos luego =git push=, nos pedirá el usuario y contraseña por https:
#+BEGIN_EXAMPLE
Username for 'https://github.com': usuarix
Password for 'https://usuarix@github.com': 

#+END_EXAMPLE

* Crear un repositorio

** Opción GitHub al final

Podemos iniciar el proyecto git en un directorio cualquiera, ya creado, o bien crearlo en uno nuevo.

*** Nuevo repositorio en directorio nuevo

 Si queremos crearlo en uno nuevo, iniciamos el repositorio con la opción =init= seguida del nombre del directorio:

 #+BEGIN_SRC sh
 git init nombre_repo
 #+END_SRC

*** Nuevo repositorio en directorio existente

 También podemos crear un directorio con =mkdir= y luego inicializar ese directorio solo con la opción =init=:

 #+BEGIN_SRC sh
 mkdir nombre_directorio
 cd nombre_directorio
 git init
 #+END_SRC

*** Pasarlo a GitHub

Para que el repositorio o proyecto también esté en GitHub, vamos a Github y creamos un proyecto nuevo que llamamos con el nombre del directorio que hemos creado o del directorio que ya existía.

#+BEGIN_QUOTE
No marques la opción /Initialize with README/ y tampoco le asignes licencia, vamos a crear un repositorio vacío para que nos sea más fácil realizar el primer =push=.

#+END_QUOTE

Conectamos el directorio local donde nos encontramos con GitHub de la siguiente manera:

#+BEGIN_SRC sh
git remote add origin https://github.com/cuenta/nombre-repositorio
 #+END_SRC

Donde le decimos a =git= que añadimos un =git= remoto en la URL de GitHub.

Hemos de crear al menos un archivo README.md donde puedes poner la información del proyecto:

#+BEGIN_SRC sh
echo "# Otro proyecto ni más ni menos" >> README.md
#+END_SRC

Añadimos el archivo a git:

#+BEGIN_SRC sh
git add README.md
#+END_SRC

Lo comiteamos:
#+BEGIN_SRC sh
git commit -m "mi primer commit"
#+END_SRC

Y lo subimos a GitHub:
#+BEGIN_SRC sh
git push -u origin master

#+END_SRC
** Opción GitHub

Primero creas un repositorio con un nombre en Github.

Github te sugiere varias formas de copiarlo en local, en el propio ordenador. Os recomiendo seguir estos pasos:

#+BEGIN_SRC sh
echo "# Proyecto de ..." >> README.md
git init
git add README.md
git commit -m "primer commit"
git remote add origin https://github.com/cuenta/nombre-repositorio
git push -u origin master
#+END_SRC

** Comprobaciones

Comprobamos su estado con la opción =status=:

#+BEGIN_SRC sh
git status
#+END_SRC

Si listamos el directorio, comproboremos que tenemos un directorio oculto llamado =.git=

#+BEGIN_SRC sh
ls -la
#+END_SRC

Cuando quieras que el directorio deje de ser un repositorio git, tan solo hay que borrar este directorio oculto con =rm -rf=:

#+BEGIN_SRC sh
rm -rf .git
#+END_SRC

Si en este caso podríamos saber el /status/ de git, el mensaje nos avisaría diciendo que no se trata de un repositorio git.

* Clonar un repositorio

Vamos a cualquier proyecto de GitHub y copiamos la URL que aparece en la casilla de *HTTPS*. En este caso, vamos a clonar el proyecto Boilerplate de Paul Irish:

#+BEGIN_SRC shell
git clone git://github.com/paulirish/html5-boilerplate.git
#+END_SRC

Si en vez de clonar un repositorio lleno queremos hacerlo vacío, hay que poner:

#+BEGIN_SRC shell
git clone --bare https://github.com/exampleuser/old-repository.git

#+END_SRC

* Estado del repositorio

Podemos ver el estado del repositorio con la opción =log=

#+BEGIN_SRC sh
git log
#+END_SRC

Que nos da toda esta información:

- La lista de cada =commit=
- El /hash/ /SHA1/ del /commit/, una cadena única de cada /commit/
- La autoría
- El mensaje que describía el cambio

* Información de cambios en el repositorio

Si queremos ver los cambios en esta versión, debemos utilizar la opción =diff=:

#+BEGIN_SRC sh
git diff
#+END_SRC

* Añadir y modificar documentos
** Añadir

#+BEGIN_SRC sh
git add ruta-nuevos-archivos
git commit -m "comentario sobre cambios"
git push -u origin rama
#+END_SRC

* Renombrar archivos o directorios

*** Renombrar un archivo

#+BEGIN_SRC sh
git mv archivo1 archivo2
git add archivo2
git push -u origin master
#+END_SRC

*** Renombrar un directorio

#+BEGIN_SRC sh
git mv directorio1 directorio2
git add directorio2
git push -u origin master
#+END_SRC

Ver los cambios que vamos a realizar con la opción =-n=, el atajo de =--dry-run=

#+BEGIN_SRC sh
git mv -n nombre_directorio_antiguo nombre_directorio_nuevo
#+END_SRC

*** Case sensitive

Renombrar en sistemas que no distinguen entre mayúsculas y minúsculas, puede dar un error cuando modifiquemos el nombre por caracteres en mayúsculas, por lo que tendríamos que hacer:

#+BEGIN_SRC sh
git mv directorio1 tempname && git mv tempname Directorio2
#+END_SRC

Si nuestro sistema no es /case sensitive/, puede ocurrir que queramos tener dos ficheros que se llaman igual, pero uno emplea mayúsculas y otro minúsculas, y git no nos lo deje incluir.

Por ejemplo, si tenemos =TFM.html= y =tfm.html= en local, y añadimos a git uno de ellos, luego no podremos añadir el otro a no ser que configuremos nuestro git como /case sensitive/:

#+BEGIN_SRC sh
git config core.ignorecase false
#+END_SRC

Ahora ya podremos hacer =git add= con éxito.

La solución viene de [[http://stackoverflow.com/questions/17683458/how-do-i-commit-case-sensitive-only-filename-changes-in-git][Stackoverflow]]

*** Borrar del repositorio

Borrar un archivo del repositorio sin borrarlo del sistema de directorios local:

#+BEGIN_SRC sh
git rm --cached archivo.org

#+END_SRC

*** Borrar un directorio

Para borrar un directorio:
#+BEGIN_SRC sh
git rm --cached -r directorio

#+END_SRC

* Actualizar repositorio

Si queremos actualizar el repositorio con los cambios que se hayan producido en él, lo haremos con la opción =pull=:

#+BEGIN_SRC sh
git pull
#+END_SRC

* Deshacer cambios

Si realizamos un =commit= pero queremos volver atrás, si no hemos realizado push, es:

#+BEGIN_SRC sh
git reset --hard HEAD-1

#+END_SRC

Si hemos realizado el =commit= lo mejor es hacer [[http://schacon.github.io/git/git-revert.html][revert]], tal como explican 


#+begin_example
git revert <hash-or-ref>
#+end_example

Para saber el =hash-or-ref= se puede consultar la parte de Commits de la página del repo o
bien con el comando ~git log --pretty=online~.

Luego hacemos ~git push~ para actualizar el repositorio con el cambio revertido.

* Pull request

Haremos un /pull request/ cuando queramos contribuir con nuestros cambios -mejoras, corrección de errores, actualizaciones- a un repositorio que ya existe.

Por eso, lo primero que tenemos que hacer es crear una copia del proyecto:

#+BEGIN_SRC sh
git clone ruta-proyecto.git

#+END_SRC

Luego creamos una rama donde hacer las modificaciones:

#+BEGIN_SRC sh

git checkout -b nueva-rama

#+END_SRC

Al crearla nos movemos a esa rama. Podemos comprobarlo si tenemos el asterisco en la rama deseada:

#+BEGIN_SRC sh
git branch
#+END_SRC

Si no estamos ahí, vamos con:
#+BEGIN_SRC sh 
git checkout nueva-rama
#+END_SRC

Luego hacemos las modificaciones que sean a nuestros archivos, las añadimos, las comiteamos y las subimos a la rama creada:

#+BEGIN_SRC sh
git add ruta-nuevos-archivos
git commit -m "comentario sobre cambios"
git push -u origin nueva-rama
#+END_SRC

Comprobamos el estado de git con =git status=

#+BEGIN_SRC sh
git status
#+END_SRC

Si todo está bien, vamos a nuestra copia del proyecto en Github y en la página del repo pondrá que hay una rama sobre la que hacer un /pull-request/, pinchamos y seguimos los pasos.

Si no hay discusión, si está todo bien, el administrador lo aprobará y entonces podremos borrar la rama. Nos movemos a master y desde ahí borramos en local y en el servidor:

#+BEGIN_SRC sh
git checkout master
git branch -d nueva-rama
git push origin --delete nueva-rama

#+END_SRC

* Ramas
Ver en qué rama estamos,  se marca con * en la que estoy 
#+BEGIN_SRC sh
git branch
#+END_SRC

Crear una rama nueva 
#+BEGIN_SRC sh
git branch nombreRama
#+END_SRC

Crear una rama apuntando a un commit
#+BEGIN_SRC sh
git checkout nombreRama sha1DelCommit
#+END_SRC 

Moverse entre ramas 
#+BEGIN_SRC sh
git checkout nombreRama
#+END_SRC

Crear rama y moverse a ella del tirón
#+BEGIN_SRC sh
git checkout -b nombreRama
#+END_SRC

Volver a Main
#+BEGIN_SRC sh
git checkout main
#+END_SRC

Ver ramas identificadas por colores
#+BEGIN_SRC sh
git log --graph  
#+END_SRC

Incorpora todos los commits a mi código master que estén en la rama que corrija el bug
#+BEGIN_SRC sh
git merge nombreRama 
#+END_SRC 

Borrar rama 
#+BEGIN_SRC sh
git branch -d nombreRama
#+END_SRC


Me dice la rama en la que estoy
#+BEGIN_SRC sh
git branch 
#+END_SRC

* Blame

Blame significa "culpable", y esta herramienta nos permite filtrar en una porción de código, quién ha introducido en el programa una nueva mejora o un error. 

#+BEGIN_SRC sh
git blame nombreFichero

git blame -L 6,8 nombreFichero
#+END_SRC

* Stashing

Estás picando en tu trabajo y tu jefe te dice que tienes que hacer otra cosa YA.
Vuelca en una pila temporal todos los cambios hechos desde el último `commit`.

#+BEGIN_SRC sh
git stash
#+END_SRC

realizas el trabajo importante... y vuelves a lo tuyo:  

#+BEGIN_SRC sh
git stash pop
#+END_SRC


Es una pila, por eso el pop. Se puede hacer más `stash` y el `pop` te saca el último que entra.
Para verlos todos utilizamos:

#+BEGIN_SRC sh
git stash list

git stash código
#+END_SRC

* Mantener un repositorio forkeado actualizado

Añades =remoteando= como servidor remoto:

#+BEGIN_SRC sh
git remote add remoteando https://github.com/cuenta/nombre-repositorio
#+END_SRC

Actualizas remoteando pero sin integrar los cambios
#+BEGIN_SRC sh
git fetch upstream
#+END_SRC

Integras los cambios con la versión local:

#+BEGIN_SRC sh
git pull upstream master

#+END_SRC

* Gitignore
https://git-scm.com/docs/gitignore
* Problemas
** 403 fatal: HTTP request failed
 http://stackoverflow.com/questions/7438313/pushing-to-git-returning-error-code-403-fatal-http-request-failed
 #+BEGIN_SRC sh
 git remote set-url origin https://yourusername@github.com/user/repo.git

 #+END_SRC

** git: error: src refspec master does not match any
http://stackoverflow.com/questions/10568641/git-error-src-refspec-master-does-not-match-any
#+BEGIN_SRC sh
git remote rm origin
git remote set-url origin git@....
git push -u origin master
#+END_SRC

** Please, commit your changes or stash them before you can merge.
Si alguien o tú mismo en otro equipo ha actualizado el repositorio
mientras tú trabajabas y te sale este error, sin entrar en las
opciones con las ramas, tienes [[http://stackoverflow.com/questions/15745045/how-do-i-resolve-git-saying-commit-your-changes-or-stash-them-before-you-can-me][tres opciones]]:

*** Comitear el cambio de la forma típica:
#+BEGIN_SRC sh
git commit -m "comentario"
#+END_SRC

*** Reservarlo o depositarlo en una pila o /stack/ con =stash=:

#+BEGIN_SRC sh
git stash

#+END_SRC

Ahí puedes hacer =push= y aparece en orden inverso:

#+BEGIN_SRC sh
git stash pop
#+END_SRC

*** Descartar los cambios que has hecho

#+BEGIN_SRC sh
git reset --hard

#+END_SRC

** Github fatal remote origin already exists
Encuentro respuesta en [[http://stackoverflow.com/questions/10904339/github-fatal-remote-origin-already-exists][stackoverflow]].

Versión corta de la solución: actualiza el repositorio remoto:

#+begin_src bash
git remote set-url origin https://github.com/cuenta/nombre-repositorio

#+end_src

Versión larga: tal como indica el error, ya hay un =remote= configurado con el mismo nombre por lo que puedes
añadir un nuevo =remoto= con otro nombre o actualizar el existente si no lo necesitas.

Para añadir un nuevo =remoto= vale el ejemplo de la versión corta y en vez de =origin= llámalo como quieras:

#+begin_src bash
git remote set-url como-quieras https://github.com/cuenta/nombre-repositorio
#+end_src

Y entonces podrías actualizarlo con:

#+begin_src bash
git push como-quieras master https://github.com/cuenta/nombre-repositorio
#+end_src

#+begin_notes
Recuerda que puedes ver los =remote= que tienes con ~git remote -v~
#+end_notes

* Pruebas
[[https://try.github.io/levels/1/challenges/1][Probar Git]]