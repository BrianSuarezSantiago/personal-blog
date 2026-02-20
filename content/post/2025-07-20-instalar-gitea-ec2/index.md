---
title: Cómo instalar tu propio servidor Gitea en EC2
slug: "instalar-gitea-ec2"
date: 2025-07-20T10:30:00+02:00
image: "https://d1hvrz5kjt6fwi.cloudfront.net/Blog Posts Covers/Posts Unorder/Post13.png"
draft: false
description:
tags:
  - AWS
  - EC2
categories:
  - AWS
  - Third-Party Tools on AWS
---

¿Buscas tener tu propio servidor Git? Configurar un servidor Gitea en una instancia EC2 con Amazon Linux es más sencillo de lo que parece. Sigue estos pasos para instalar y configurar Gitea y gestionar tus repositorios en la nube.

---

# Instalación de Gitea

## 1. Actualizar y preparar el entorno

1.&nbsp;Actualiza los paquetes e instala los requisitos:

```bash
sudo yum update -y
sudo yum install -y git wget mariadb105-server mariadb105 jq
```

2.&nbsp;Configura y habilita MariaDB:

```bash
sudo systemctl enable mariadb
sudo systemctl start mariadb
sudo mysql_secure_installation
```

---

## 2. Configurar la base de datos

1.&nbsp;Accede a MariaDB:

```bash
sudo mysql -u root -p
```

2.&nbsp;Crea una base de datos y usuario para Gitea:

```sql
CREATE DATABASE gitea;
CREATE USER 'gitea'@'localhost' IDENTIFIED BY 'tu-contraseña';
GRANT ALL PRIVILEGES ON gitea.* TO 'gitea'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## 3. Instalar Gitea

1.&nbsp;Descarga el binario de Gitea:

```bash
wget -O gitea https://dl.gitea.io/gitea/1.21.4/gitea-1.21.4-linux-amd64
```

2.&nbsp;Asigna permisos y mueve el binario:

```bash
chmod +x gitea
sudo mv gitea /usr/local/bin/
```

3.&nbsp;Crea directorios necesarios:

```bash
sudo mkdir -p /var/lib/gitea/{custom,data,log}
sudo chown -R ec2-user:ec2-user /var/lib/gitea
sudo chmod -R 750 /var/lib/gitea
sudo mkdir -p /etc/gitea
sudo chown -R ec2-user:ec2-user /etc/gitea
```

---

## 4. Crear un servicio para Gitea

1.&nbsp;Crea un archivo de servicio en systemd:

```bash
sudo nano /etc/systemd/system/gitea.service
```

2.&nbsp;Añade la siguiente configuración:

```ini
[Unit]
Description=Gitea
After=syslog.target
After=network.target
After=mariadb.service

[Service]
RestartSec=2s
Type=simple
User=ec2-user
Group=ec2-user
WorkingDirectory=/var/lib/gitea
ExecStart=/usr/local/bin/gitea web --config /etc/gitea/app.ini
Restart=always
Environment=USER=ec2-user HOME=/var/lib/gitea GITEA_WORK_DIR=/var/lib/gitea

[Install]
WantedBy=multi-user.target
```

3.&nbsp;Guarda, recarga y habilita el servicio:

```bash
sudo systemctl daemon-reload
sudo systemctl enable gitea
sudo systemctl start gitea
```

---

## 5. Configurar Gitea desde el navegador

- Abre un navegador y ve a: `http://<tu-ip-pública>:3000`.
- Completa la configuración:
  - **<u>Base de datos:</u>**
      - Tipo: MySQL.
      - Host: `127.0.0.1:3306`.
      - Nombre: `gitea`.
      - Usuario: `gitea`.
      - Contraseña: Tu contraseña configurada.
      - **URL**: `http://<tu-ip-pública>:3000`.

---

## 6. Redirigir tráfico al puerto 80 (Opcional)

1.&nbsp;Haz que Gitea esté disponible en el puerto 80 con `iptables`:

```bash
sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 3000
```

2.&nbsp;Guarda los cambios para que sean persistentes:

```bash
sudo yum install -y iptables-services
sudo service iptables save
```

---

# ¡Todo listo!

Ahora tienes un servidor Gitea corriendo en tu instancia EC2. ¿Qué sigue? Agrega tus repositorios y comienza a colaborar.

Si tienes preguntas o quieres mejorar la configuración, ¡déjalo en los comentarios! 👇🏻
