# Demo de CI/CD

## Instalación

### Host

* Agregar una línea en el archivo */etc/hosts* una línea con el valor :
```
{Dirección IP actual}  dockerhub kubehost gitlab.interware.mx nexus.interware.mx jenkins.interware.mx sonarqube.interware.mx
```

* En el directorio raiz del proyecto *cicd-tools*, ejecutar:
```
bin/build-jenkins
bin/build-nexus
bin/build-sonarqube
bin/build-gitlab

bin/start-jenkins
bin/start-nexus
bin/start-sonarqube
bin/start-gitlab
```

* En el browser, ingresar a:
```
http://jenkins.interware.mx:8080
http://gitlab.interware.mx:9080
http://sonarqube.interware.mx:9000
http://nexus.interware.mx:8081
```

* En el directorio raiz del proyecto *cicd-tools*, ejecutar:
```
docker stop jenkins ; docker rm jenkins
docker stop nexus ; docker rm nexus
docker stop sonarqube ; docker rm sonarqube
docker stop gitlab ; docker rm gitlab
```

* Ejecutar:
```
docker run --detach -p 5000:5000 --name registry registry
```
* Ejecutar:
```
sudo iptables -A INPUT -i docker0 -j ACCEPT
```
* Ejecutar:
```
sudo chmod 666 /var/run/docker.sock
```

* En el directorio *docker-compose* del proyecto cicd-tools, ejecutar:
```
docker-compose up
```

### Jenkins
* Ingresar a consola web de jenkins, *http://jenkins.interware.mx:8080*, usando el password inicial autogenerado. Para obtener el password, ejecutar:
```
docker exec -it jenkins bash
cat /var/jenkins_home/secrets/initialAdminPassword
exit
```
* Dar permisos al socker de docker 
```
docker exec -u 0 -it jenkins bash
chmod 666 /var/run/docker.sock
exit
```
* Installar los plugins default haciendo click en *Install Suggested Plugins*

* Crear el usuario administrativo:
Username : *jenkinsadmin*
Password : *admin*
Full name: *Jenkins Administrator*
E-mail address: *{email@site.com}*

### Gitlab

* Acceder a *http://gitlab.interware.mx:9080*

* Inicialización de usuario root usando el password *!1nt3rw4r3#*

* Ingresar con el usuario *root*

* Logout de consola administrativa de Gitlab

* Registrar nuevo usuario en Gitlab presionando *Register* en la pantalla de *Login* con los siguientes datos:
Full name: *Interware*
Username: *interware*
Email: *{email@site.com}*
Password: *!1nt3rw4r3#*

* Acceder a *http://gitlab.interware.mx:9080* utilizando el usuario *root*

* Crear el grupo privado *devops*

* Crear el proyecto privado *cicd-demo* dentro del grupo *devops* seleccionando también *Initialize a repository with a README*

* Otorgar permisos de *owner* al usuario *interware* en el grupo *devops*, entrando a *Groups > Your groups > devops > Members*

* Logout de consola administrativa de Gitlab

* Ingresar con el usuario *interware*

* Seleccionar el proyecto *cicd-demo* y crear la rama *develop* a partir de la rama *master* entrando a *Repository > Branches > New branch*

* Hacer que la rama principal sea *develop* entrando a *Settings > Repository > Default Branch*

* Crear la rama *feature/initial_commit* a partir de *develop* entrando a *Repository > Branches > New branch*

### SonarQube
* Acceder a *http://sonarqube.interware.mx:9000*

* Login con *admin/admin*

* Entrar a *Administration > Marketplace > Code Smells -> Install*

* Reiniciar el servidor Sonarqube, presionando el botón *Restart*

### Nexus
* Acceder a *http://nexus.interware.mx:8081*

* Login con los datos:
Username: *admin*
Password: *admin123*

* Cambiar contraseña del usuario *admin* ingresando a *admin > Change password* con el nuevo valor *!1nt3rw4r3#*

## Configuración adicional

## Host
* Reiniciar los servicios, ejecutando, en el directorio *docker-compose* del proyecto *cicd-tools*, el comando:
```
docker-compose down
docker-compose up
```
* Acceder a *http://sonarqube.interware.mx:9000*

* Crear el proyecto cicd-demo, entrando a *Projects > Create new project* usando los datos:
Project key: *cicd-demo*
Display name: *cicd-demo*
Click en *Generate*
Click en *Continue*
What is your project's main language: *Java*
technology: *Maven*
Click en *Copy* (Guardar en un txt lo que copio al portapapeles)

* Acceder a *http://jenkins.interware.mx:8080*

* Instalar plugins adicionales de Jenkins entrando a *Manage Jenkins > Manage Plugins > Available*. Los plugins a instalar son:
  ** *Maven Integration*
  ** *SonarQube Scanner*
  ** *Docker*
  ** *Gitlab*
  ** *JaCoCo*
  ** *Nexus Platform*
  ** *JUnit Attachments*
  ** *JUnit Realtime Test Reporter*
  ** *Ansible*
  ** *Ansible Tower*

* Configuración de Maven en Jenkins
Ingresar a Manage Jenkins > Global Tool Configuration > Maven > Add Maven
Nombre:apache-maven-3
Opción selecionada->Instalar automáticamente
Versión->3.6.3

* Configuración de Nexus en Jenkins
Ingresar Manage Jenkins > Configure System > Sonatype Nexus
Display Name > Nexus DevOps Release
Server ID->nexus-devops-release
Server URL->http://nexus:8081
*Add Credentials*
Domain->Global credentials (unrestricted)
Kind->Username with password
    Username->admin
    Password->!1nt3rw4r3#
    ID->nexusAdmin
    Description->Nexus Administrator
* Botón *Guardar*
* Crear pipeline ingresando a *New Item > Pipeline* con el nombre *cicd-demo*

* Agregar usuario para acceso al SCM Gitlab. Para lo anterior ingresar a *Jenkins > Credentials > System > Global credentials (unrestricted) > Add Credentials*. Usar los siguientes valores:
Username: *interware*
Password: *!1nt3rw4r3#*
ID: *gitlab-interware*
Description: *Usuario para acceso a Gitlab* 

* Agregar usuario para acceso ssh al Host. Para lo anterior ingresar a *Jenkins > Credentials > System > Global credentials (unrestricted) > Add Credentials*. Usar los siguientes valores:
Username: *[UsuarioHost]*
Password: *[PasswordHost]*
ID: *ssh-host*
Description: *Usuario ssh para acceso al Host*

* Agregar usuario para acceso al SonarQube. Para lo anterior ingresar a *Jenkins > Credentials > System > Global credentials (unrestricted) > Add Credentials*. Usar los siguientes valores:
Kind : *Secret Text*
Secret: *4fd45814cf4c4f1c6830f9e552b84c0276ed6f68* (Usar el token guardado anteriormente)
ID: *sonarqube-admin*
Description: *Token para acceso a SonarQube* 

* Configurar SCM. En la sección *Build Triggers* seleccionar *Poll SCM* e ingresar el valor '\*/5 \* \* \* \*' en el campo *Schedule*. Presionar el botón *Apply*

* En la sección *Pipeline*, seleccionar *Pipeline script from SCM* en el combo-box *Definition*. Ingresar además los siguientes datos:
SCM: *Git*
Repository URL: *http://gitlab/devops/cicd-demo.git*
Credentials: *interware (Usuario para acceso a Gitlab)*
Branch Specifier: *\*/develop*
Script Path: *Jenkinsfile*

* Configurar acceso a SonarQube en Jenkins entrando a *Jenkins > Manage Jenkins > Configure System > SonarQube servers > Add SonarQube* usando los siguientes datos:
Name: *sonarqube*
Server URL: *http://sonarqube:9000*
En *Server authentication token* seleccionar *Token para acceso a SonarQube*

* Configuración nuevo repositorio en Nexus
Ir a Server administration and Configuration > Repositories > Create repository
    Recipe->maven2(hosted)
    Name->devops-release
    Create repository

* Entrar al contenedor de Jenkins 

* Ingresar como root
```
docker exec -u 0 -it jenkins bash
```

* Ejecutar la siguiente sentencia cada vez que se reinicie docker-compose
```
su -
echo "$(ip route show | awk '/default/ {print $3}') kubehost" >> /etc/hosts
exit
```

* En el Host ejecutar los siguientes comandos*
```
su -
cd /etc/docker
```

* Si no existe el archivo daemon.json debe crearse
```
vi daemon.json
{ "insecure-registries":["dockerhub:5000"] }
exit
```

* Detener docker-compose
```
docker-compose down
```

* Reiniciar el servicio de docker
```
sudo /etc/init.d/docker restart
```

* Registrar la llave en authorized_keys en el host

* Ingresar a la ruta
```
cd [rutaInicial]/cicd-tools/docker-images/jenkins
mkdir ~/.ssh
mkdir ../../docker-compose/jenkins/data/.ssh
cat id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

* Agregar llaves al contenedor jenkins
*Proyecto cicd_tools >docker-images >jenkins*

* Ejecutar los siguientes comandos
```
docker cp id_rsa jenkins:/var/jenkins_home/.ssh
docker cp id_rsa.pub jenkins:/var/jenkins_home/.ssh
```

* Acceder al contenedor jenkins
```
docker exec -it jenkins bash
cd /var/jenkins_home/.ssh
chmod 400 id_rsa
chmod 400 id_rsa.pub
```

* Validar el acceso al host
```
ssh [USUARIO_HOST]@kubehost
```

* Modificar los datos del archivo Jenkinsfile del proyecto que se subio al gitlab local
git clone http://gitlab.interware.mx:9080/devops/cicd-demo
usuario:interware
password:!1nt3rw4r3#

Descomprimir zip de la memoria (cicd-demo.zip) en maquina local:

cp -R [ruta_carpeta_cicd-demo]/* [carpeta_donde_se hizo el git clone]/
cd [carpeta_donde_se hizo el git clone]
git status
git add . -A
git commit -m "Mi primer commit"
git checkout develop
git push origin develop

* En la parte de stage('Deploy') realizar las siguientes modificaciones*
```
becomeUser: *'[USUARIO_SSH_HOST]'*, \
credentialsId: 'ssh-host', \
```

* Agregar la siguiente línea despúes de la anterior
```
hostKeyChecking: false, \
```
## Usuarios/Paswords

* Jenkins
```
Username : jenkinsadmin
Password : admin
```

* Sonar
```
Username : admin
Password : admin
```

* Nexus
```
Username: admin
Password: !1nt3rw4r3#
```

* Gitlab
```
Username : root
Password : !1nt3rw4r3#
```

```
Username : interware
Password : !1nt3rw4r3#
```

