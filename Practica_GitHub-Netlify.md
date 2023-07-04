# PRÁCTICA - SUBIR PÁGINA ESTÁTICA A GITHUB (comandos).

### Para empezar a subir nuestra página estática a GitHub, comenzamos metiéndonos desde el GitCMD en la ruta de nuestra página estática.
### Y una vez dentro de la ruta, creamos el nuevo repositorio con un:
```
git init
```
### A continuación, añadimos el contenido al nuevo repositorio, con el comando:
```
git add . # el punto hace referecia al directorio actual
```
### Comiteamos con un comentario:
```
git commit -m “comentario”
```
### Por último, para conectarnos con el GitHub remoto, realizamos los siguientes comandos:
### (poniendo la URL de nuestro repositorio de GitHub)
```
git remote add origin https://github.com/mjrodriu/paginaestatica.git
git branch -M main # este comando se utiliza para cambiar el nombre de la rama principal (master) a "main" del repositorio.
```

### Y para finalmente subirlo, hacemos un push:
```
git push -u origin main
```

### A continuación, entramos a la plataforma de Netlify, desde donde vamos a subir nuestra página estática, 
### seleccionamos el repositorio de GitHub donde tenemos los archivos subidos anteriormente: 
