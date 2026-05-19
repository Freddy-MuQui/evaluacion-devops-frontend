# Tienda de Alimentos para Perritos - Frontend 🐶

Este repositorio contiene la capa de presentación de la aplicación **Tienda de Alimentos para Perritos**. Está construida como una aplicación web estática y distribuida utilizando un servidor de alto rendimiento **Nginx** dentro de un contenedor Docker.

## 🏗️ Rol en la Arquitectura
El Frontend actúa como la única capa pública de la infraestructura en AWS. Está alojado en una instancia EC2 dentro de una subred pública para permitir el acceso a los usuarios a través del puerto `80` (HTTP). Se comunica internamente con la capa de Backend utilizando enrutamiento privado dentro de la VPC.

## 🛠️ Tecnologías Utilizadas
* **Servidor Web:** Nginx (Imagen basada en Alpine Linux)
* **Contenedorización:** Docker
* **Automatización:** GitHub Actions (CI/CD)
* **Registro de Imágenes:** Docker Hub

## ⚙️ Configuración del Servidor (`default.conf`)
El archivo de configuración de Nginx redirige las peticiones de la API hacia el Backend en la subred privada. Asegúrate de definir la IP privada correcta de la instancia de Backend antes de compilar:

```nginx
server {
    listen 80;
    server_name localhost;

    location / {
        root /usr/share/nginx/html;
        index index.html;
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://<IP_PRIVADA_BACKEND>:3001/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

🚀 Despliegue Manual con Docker
Para levantar este servicio de forma manual, se utilizan los siguientes comandos:
# 1. Construir la imagen
sudo docker build -t frontend .

# 2. Levantar el contenedor en el puerto 80
sudo docker run -d -p 80:80 --name contenedor_frontend frontend

🤖 CI/CD (Despliegue Continuo con GitHub Actions)
Este repositorio cuenta con un pipeline de automatización configurado en la rama deploy. Al realizar un git push hacia esta rama, GitHub Actions se encarga de:

Hacer login en Docker Hub.

Construir la imagen y subirla al registro público.

Conectarse por SSH a la instancia EC2 pública.

Descargar la imagen actualizada y reiniciar el contenedor automáticamente.
