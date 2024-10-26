
# Página web con Flask
Es una página web sencilla hecha con Flask, HTML y CSS.

## Requisitos previos:
- Python 3 debe estar instalado.
- Flask, un microframework de Python para el desarrollo web.

## Instalación de dependencias:
1. Instalar Flask: En tu terminal o en Visual Studio Code, ejecuta:
```
pip install Flask
```

## Ejecución de la página web:
Para ejecutar la aplicación Flask:
1. Crea un carpeta llamada *video* dentro de la carpeta *static* y coloca ahí un vídeo que tenga como nombre *videoStreaming.mp4* para que funcione correctamente.
2. Abre la terminal y navega hasta el directorio del proyecto donde se encuentra el archivo app.py.
3. Inicia la aplicación Flask con uno de los siguientes comandos:
```
python app.py
python3 app.py
```
4. Acceso local: La página web será accesible en http://127.0.0.1:5000.

## Acceso público a la página web con Ngrok:
Para compartir tu servidor local a través de una URL pública usando Ngrok:
1. Instala Ngrok en tu sistema (si no lo tienes ya instalado):
```
sudo snap install ngrok
```
2. Añade tu token de autenticación de Ngrok (regístrate en Ngrok para obtener uno):
```
ngrok config add-authtoken TU_AUTHTOKEN
```
3. Inicia un túnel HTTP en el puerto 5000 para que tu servidor Flask sea accesible públicamente:
```
ngrok http 5000
```
Ngrok generará una URL pública que podrás compartir para que otros accedan a tu aplicación web.