# PracticaCalificada5

# Pregunta 1
## Actividad 23

### Introducción a las pruebas de aceptación (UAT)
La prueba de aceptación es una paso que se realiza para determinar si se cumple con los requisitos comerciales o los contratos.
El primer aspecto que necesitamos es el Docker Registry

### Instalación y uso del Docker Registry
Es una aplicación de servidor sin estado (stateless), que nos permitirá publicar y recuperar imágenes.

### El repositorio de artefactos
El repositorio de artefactos se dedica a almacenar artefactos binarios de software. Este juega un papel muy importante en el proceso de CD porque nos permitirá garantizar el uso del mismo binario en todos el proceso del pipeline

### Instalación de un registro Docker
Tenemos dos opciones disponibles, un Docker Registry basado en la nube y un Docker registry hospedado

#### Docker Registry en la nube
El beneficio de utilizar esta opción, es que no necesitamos mantener nada por nuestra cuenta, ya que, todo es mantenido por el servicio en la nube. El más popular es Docker Hub.

#### Docker Hub
Lo único que necesitamos para utilizar Docker Hub es crear una cuenta:
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/docker_hub_captura.PNG "docker hub account")

#### Uso del Docker Registry
Cuando nuestro registro esté configurado, podemos trabajar con él en tres etapas, de la sgte manera:
- Construcción de una imagen
- Realizamos un pushing de la imagen en el registro (Subimos la imagen)
- Realizamos un pulling de la imagen del registro (Bajar la imagen del Docker Hub)

#### Construyendo la imagen
- Primero crearemos un directorio llamado "trabajando con docker registry" y dentro de este colocaremos el Dockerfile

![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/trabajando_con_docker_registry.PNG "")

- Crear el archivo Dockerfile, tomar como imagen base Ubuntu e instalar el intérprete de Python
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/nano_Dockerfile.PNG "")

![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/nano_Dockerfile2.PNG "")

- Ahora construimos la imagen con el comando "docker build -t ubuntu_with_python . "
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/build_image_1.PNG "")

#### Realizando un pushing de la imagen en el registor
- Para enviar la imagen creada, debemos etiquetarla de acuerdo con una convención de nomenclatura <registry_address>/<image_name>:<tag>. Para nuestro caso, que usaremos Docker Hub, el registry_address debe ser nuestro nombre de usuario de Docker Hub
- Etiquetemos la imagen para usar Docker Hub, usando el sgte comando:
"docker tab ubuntu_with_python rlov/ubuntu_with_python:1
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/tag_rlovuni.PNG "")
 
- Ahora, podemos almacenar la imagen en el registro usando el comando push, de la sgte manera:
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/push_docker.PNG "")
  
- Y podemos verificarlo en la interfaz web de Docker Hub
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/docker_hub_image.PNG "")
  
#### Realizamos un pulling de la imagen del registro
- Eliminamos la imagen localmente
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/delete_local_image.PNG "")
- Recuperamos la imagen del registro
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/pull_docker.PNG "")
 
 
### Pruebas de aceptación en el pipeline de Jenkins
El proceso es así:
1. El desarrollador envía un cambio de código a Github
2. Jenkins detecta el cambio, activa la compilación y verifica el código actual
3. Jenkins ejecuta la fase de commit y crea la imagen de Docker
4. Jenkins envía la imagen al Docker registry
5. Jenkins ejecuta el contenedor Docker en el entorno de prueba
6. El host de Docker en el entorno de pruebas debes realizar pull de la imagen del Docker registry
7. Jenkins ejecuta el conjunto de pruebas de aceptación contra la aplicación que se ejecuta en el entorno de pruebas

### La etapa de construcción de Docker
#### Agregar un archivo Docker
- Vamos a crear un Dockerfile en el directorio raíz del proyecto calculadora (https://github.com/ricardoolivaresventura/calculador)
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/add-dockerfile2.PNG "")

- Ahora compilamos con el comando ./gradlew build
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/gradlew-build.PNG "")

- Creamos la imagen
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/build-calculador.PNG "")

-Y lo subimos al repositorio donde tenemos el proyecto de calculador de la actividad pasada
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/repository-dockerfile.PNG "")
 
#### Agregar la compilación de Docker al pipeline
- El paso final que debemos realizar es agregar la etapa Docker build al Jenkinsfile:
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/jenkinsfile.PNG "")

- Cuando la imagen esté lista, podemos almacenarla en el registro, para ello agregamos la etapa Docker push
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/jenkinsfile-dockerpush.PNG "")
 
 
#### La etapa de prueba de aceptación
Para realizar las pruebas de aceptación, primero debemos implementar la aplicación en el entorno de prueba y luego ejecutar el conjunto de pruebas de aceptación

#### Adición de una implementación provisional al pipeline
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/deploy-stg.PNG "")

#### Agregar una prueba de aceptación al pipeline
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/acceptante-test.PNG "")
 
#### Agregar una etapa Acceptance test
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/acceptante-test-jenkinsfile.PNG "")
 
#### Adición de un entorno de etapa de limpieza
Ahora agregaremos una sentencia de que SIEMPRE luego de ejecutar toda la construcción procederemos a limpiar el entorno de pruebas, para ello usaremos "post" y "always"
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/add-always.PNG "")
 
#### Escribir pruebas orientadas al usuario
Para definir lo que tendrá un software, se necesita la opinión de los usuarios y a su vez a los desarrolladores que son los que llevarán dichos requerimientos a código. Por ello, necesitamos un lenguaje común para colaborar, en este caso utilizaremos cucumber para crear pruebas de aceptación
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/add-feature.PNG "")
 
 #### Creación de definiciones de pasos
 - En este paso crearemos la clase StepDefinitions usando cucumber
 ![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/step-definitions.PNG "")
 
 #### Ejecución de una prueba de aceptación automatizada
 - Agregamos las librerías en el archivo build.gradle y además agregaremos un código que dividirá las pruebas en pruebas unitarias y pruebas de aceptación
 ![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/add-cucumber.PNG "")

- Ahora, agregaremos un JUnit Test Runner llamado AcceptanceTest.java
![Alt text](https://raw.githubusercontent.com/ricardoolivaresventura/PracticaCalificada5/main/test-runner.PNG "")
 

