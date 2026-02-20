---
title: Cómo instalar Docker en una instancia EC2 con Amazon Linux 2023 y Ubuntu
slug: "instalar-docker-ec2"
date: 2025-06-17T10:30:00+02:00
image: "https://d1hvrz5kjt6fwi.cloudfront.net/Blog Posts Covers/Posts Unorder/Post9.png"
draft: false
description:
tags:
  - AWS
  - EC2
categories:
  - AWS
  - Third-Party Tools on AWS
---

Docker es una plataforma increíble que te permite empaquetar, desplegar y ejecutar aplicaciones en contenedores. Estos contenedores son ligeros, portátiles y consistentes, lo que hace que el desarrollo y despliegue de software sean mucho más eficientes.

En este artículo, te mostraré cómo instalar Docker en una instancia de AWS EC2 usando dos sistemas operativos populares: **Amazon Linux 2023** y **Ubuntu**. También incluiré consejos útiles para diagnosticar problemas comunes con contenedores.

---

## Configura tu instancia EC2

Antes de comenzar, asegúrate de tener:
- Una instancia de EC2 lanzada con acceso SSH habilitado.
- Permisos de administrador para instalar paquetes y configurar el sistema.

---

# Instalación en Amazon Linux 2023

1.&nbsp;Actualiza tu sistema

Es importante tener el sistema actualizado antes de instalar Docker:

```bash
sudo dnf update -y
```

2.&nbsp;Instala Docker

Amazon Linux 2023 utiliza el administrador de paquetes `dnf`. Instala Docker con:

```bash
sudo dnf install docker -y
```

3.&nbsp;Inicia y habilita Docker

Después de instalar Docker, debes iniciar el servicio y habilitarlo para que se ejecute automáticamente al reiniciar:

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

4.&nbsp;Verifica la instalación

Asegúrate de que Docker esté instalado y en ejecución:

```bash
docker --version
sudo systemctl status docker
```

5.&nbsp;Añade tu usuario al grupo Docker

Esto evita que tengas que usar `sudo` cada vez que ejecutas un comando de Docker:

```bash
sudo usermod -aG docker $USER
# Cierra sesión y vuelve a iniciar para aplicar los cambios.
```

---

# **Instalación en Ubuntu**

1.&nbsp;Actualiza tu sistema

Asegúrate de que tu sistema tenga las últimas actualizaciones:

```bash
sudo apt update && sudo apt upgrade -y
```

2.&nbsp;Instala dependencias necesarias

Docker requiere ciertos paquetes adicionales para funcionar correctamente:

```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
```

3.&nbsp;Añade la clave GPG y el repositorio oficial de Docker

Esto asegura que instalaremos la última versión estable de Docker:

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

4.&nbsp;Instala Docker

Actualiza los paquetes disponibles e instala Docker:

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y
```

5.&nbsp;Inicia y habilita Docker

Igual que en Amazon Linux, inicia y habilita Docker:

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

6.&nbsp;Añade tu usuario al grupo Docker

Al igual que en Amazon Linux:

```bash
sudo usermod -aG docker $USER
# Cierra sesión y vuelve a iniciar para aplicar los cambios.
```

---

# Diagnóstico de problemas comunes con contenedores

A veces, los contenedores pueden presentar problemas durante su creación o ejecución. Aquí hay algunos pasos útiles para identificar y solucionar errores:

### Verifica el estado del contenedor

Asegúrate de que el contenedor se haya creado y revisa su estado:

```bash
docker ps -a
```

Busca en la columna `STATUS` algo como `Exited (1)` o similar. Esto indica que el contenedor intentó ejecutarse pero falló.

---

### Revisa los logs del contenedor

Obtén los registros del contenedor para identificar posibles errores o advertencias:

```bash
docker logs <nombre_contenedor>
```

Esto te mostrará mensajes útiles para diagnosticar el problema.

---

### Verifica los requisitos de memoria

Algunas aplicaciones requieren una cantidad mínima de memoria para funcionar. Verifica la memoria disponible en tu instancia con:

```bash
free -h
```

Si la memoria es insuficiente, considera aumentar los recursos de tu instancia o utiliza una máquina con más capacidad.

---

### Revisa los permisos del volumen

Si tu contenedor usa volúmenes, asegúrate de que estén correctamente configurados:

```bash
docker volume inspect <nombre_volumen>
```

Esto te mostrará detalles del volumen, como su ubicación en el sistema de archivos y los permisos.

---

### Elimina y recrea el contenedor

Si sospechas que el contenedor está corrupto o mal configurado, elimínalo y vuelve a crearlo:

```bash
docker stop <nombre_contenedor>
docker rm <nombre_contenedor>
docker run -d -p <puerto_host>:<puerto_contenedor> --name <nombre_contenedor> <imagen>
```

---

### Asegúrate de tener la imagen correcta

Confirma que tienes la imagen descargada correctamente:

```bash
docker images | grep <nombre_imagen>
```

Si la imagen no aparece, descárgala de nuevo:

```bash
docker pull <imagen>
```

---

### Prueba un inicio interactivo

Ejecuta el contenedor en modo interactivo para ver errores directamente:

```bash
docker run -it <opciones> <imagen>
```

---

## Conclusión

Docker es una herramienta poderosa y versátil, pero como cualquier tecnología, puede presentar desafíos. Con estos pasos, estarás mejor preparado para instalar Docker en tu instancia EC2 y solucionar problemas comunes con contenedores.

¿Tienes preguntas o te encontraste con algún error? ¡Déjalas en los comentarios y estaré encantado de ayudarte! ☺️👇🏻
