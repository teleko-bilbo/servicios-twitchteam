## Zuhaitz
Primer intento:
Estoy intentando probar un servicio de streming video que viene en un conjunto de contenedores. Lo he descargado desde este repositorio de github: 
https://github.com/Open-Streaming-Platform/open-streaming-platform-docker.git

He clonado el repositorio y he ejecutado el comando "docker compose" 
Es un servicio muy complejo y pesado para la primera practica, pero puede que valga para la final

Segundo intento:
Este segundo contenedor es mas simple
https://github.com/stefaniuk/docker-streaming-server.git

He seguido los pasos proporcionados en el repositorio: 

```
docker build --tag codeworksio/streaming-server \
docker run --detach --restart always \
    --name streaming-server \
    --hostname streaming-server \
    --publish 1935:1935 \
    --publish 8080:8080 \
    --publish 8443:8443 \
    codeworksio/streaming-server
cd ./documents/examples
docker-compose up -d
```
Ahora hay un contenedor para servidor y otro cliente para emitir el video

Para emitir el directo se necesita una la aplicación OBS, que se descarga con los siguientes comandos
```
sudo add-apt-repository ppa:obsproject/obs-studio
sudo apt update
sudo apt install ffmpeg obs-studio
```
Con el OBS y los pasos que indica el repositorio se puede empezar el directo en la dirección http://localhost:9999

## Rubén
Estoy haciendo una página web con Flask de Python, que a su vez utiliza HTML y CSS. La idea es tener una página web con 6 vídeos en un principio de forma local a la que poder realizar consultas para poder ver el rendimiento de la misma con pocos usuarios y muchos usuarios. De esta forma, poder ver los cuellos de botella que tiene.

Toda la información sobre la página está aquí:
[Ir al README de la página web](Webpage/README.md)

He realizado diferentes pruebas de carga con JMeter para comprobar el rendimiento del servicio y también he comprobado el rendimiento de la CPU (htop) con pocos usuarios y con muchos usuarios.
