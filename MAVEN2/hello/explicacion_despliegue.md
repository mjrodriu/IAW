# Crear la estructura del proyecto:
```JAVA
mvn archetype:generate -DgroupId=org.net -DartifactId=hello -DarchetypeArtifactId=maven-archetype-webapp
```
## Con este comando queremos decir:
- archetype:generate: es el comando de maven para generar un proyecto. Por defecto crea un proyecto de java muy general.
- DgroupId: es el conjunto de proyectos al que pertenece nuestro proyecto. Por ejemplo, yo puedo meter todos mis programas de ejemplo en un grupo que llamo "org.net". 
Este nombre que pongamos aquí va a servir de paquete inicial para todas las clases del proyecto. Todos los proyectos deben pertenecer a un grupo, aunque sea único para él.
- DartifactId: es el nombre que queramos dar al proyecto. Maven creará un directorio con este nombre y el jar que genere para el proyecto tendrá también este nombre. 
- Con este comando se crea el pom.xml.

# La estructura de directorios es:

- Directorio creado para el proyecto: **MAVEN2**
- Directorio generado: *hello*
------------------------------------------------------------------
- Dentro de *hello*:

![la imagen por si no carga](https://github.com/mjrodriu/IAW/blob/main/img/arquitectura_general.PNG)

-------------------------------------------------------------------
- Dentro de *pom.xml*:

<img src="https://github.com/mjrodriu/IAW/blob/main/img/pom.PNG" align="center" height="400" width="400">

-------------------------------------------------------------------
- Dentro de **hello>target** se crea el *.war*: (el que se sube al tomcat)

![la imagen por si no carga](https://github.com/mjrodriu/IAW/blob/main/img/dentro_target.PNG)

-------------------------------------------------------------------

- Dentro de **hello>SRC>main**:

![la imagen por si no carga](https://github.com/mjrodriu/IAW/blob/main/img/detro_src_main.PNG)

-------------------------------------------------------------------

- Dentro de **hello>SRC>main>Java>org>net** está el **Hello.java** (org y net son lo del archetype y hay que crearlo antes):

![la imagen por si no carga](https://github.com/mjrodriu/IAW/blob/main/img/java_org_net.PNG)

-------------------------------------------------------------------

- Dentro de **hello>SRC>main>webapp**:

![la imagen por si no carga](https://github.com/mjrodriu/IAW/blob/main/img/webapp.PNG)

-------------------------------------------------------------------

- Dentro de **hello>SRC>main>webapp>web-inf** está el **web.xml**:

![la imagen por si no carga](https://github.com/mjrodriu/IAW/blob/main/img/webinf_webxml.PNG)

En el *WEB-XML* se incluye el servlet class: ```<servlet-class>org.net.Hello</servlet-class>``` que hace referencia al *Hello.java* creado en **main>Java>org>net** 

-------------------------------------------------------------------

# Generar el *.war*:
Ejecutando el comando: 
```java
mvn package
```
- Con este se crea la carpeta *target* y dentro estará el *.war*.
- Hay que tener en cuenta que este se debe ejecutar dentro del directorio donde está el *POM.xml*, es decir dentro de *hello*.


