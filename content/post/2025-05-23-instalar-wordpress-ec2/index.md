---
title: Cómo instalar WordPress en una instancia EC2 con Amazon Linux 2023
slug: "instalar-wordpress-ec2"
date: 2025-05-23T10:30:00+02:00
image:
draft: false
description:
tags:
  - AWS
  - EC2
categories:
  - AWS
  - Third-Party Tools on AWS
---

WordPress es uno de los sistemas de gestión de contenido más populares, y configurarlo en una instancia EC2 con Amazon Linux 2023 no es tan complicado como parece.

Aquí tienes una guía paso a paso para hacerlo realidad.

---

### Paso 1: Actualiza los paquetes de la instancia

Mantén tu sistema al día:

```bash
sudo dnf update -y
```

---

### Paso 2: Instala Apache, PHP y MariaDB

1.&nbsp;Instalar Apache

 Apache será el servidor web:

 ```bash
 sudo dnf install httpd -y
 ```

2.&nbsp;Instalar PHP

 WordPress requiere PHP (7.4 o superior)

 ```bash
 sudo dnf install php php-mysqlnd php-fpm php-xml php-json php-cli -y
 ```

3.&nbsp;Instalar MariaDB (alternativa a MySQL)

```bash
sudo dnf install -y mariadb105-server
```

4.&nbsp;Iniciar y habilitar los servicios

```bash
# Inicia y habilita Apache
sudo systemctl start httpd
sudo systemctl enable httpd

# Inicia y habilita MariaDB
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

5.&nbsp;Verificar el estado de ambos servicios:

```bash
# Verifica el estado de Apache
sudo systemctl status httpd

# Verifica el estado de MariaDB
sudo systemctl status mariadb
```

---

### Paso 3: Configura la base de datos

1.&nbsp;Accede a MySQL:

```bash
sudo mysql -u root
```

2.&nbsp;Crea una base de datos para WordPress:

```sql
CREATE DATABASE wordpress;
```

3.&nbsp;Crea un usuario y dale permisos:

> **Nota:** Sustituye los valores `'wp_user'` y `'tu_contraseña'` por los valores que deseas utilizar para el usuario y contraseña.

```sql
CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'tu_contraseña';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wp_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

### Paso 4: Descarga e instala WordPress

1.&nbsp;Instala `wget` (si no está instalado):

```bash
sudo dnf install wget -y
```

2.&nbsp;Descarga WordPress:

```bash
wget https://wordpress.org/latest.tar.gz
```

3.&nbsp;Extrae los archivos:

```bash
tar -xvzf latest.tar.gz
```

4.&nbsp;Mueve los archivos al directorio web de Apache:

```bash
sudo rsync -avP wordpress/ /var/www/html/
```

5.&nbsp;Ajusta los permisos:

```bash
sudo chown -R apache:apache /var/www/html/*
```

---

### Paso 5: Configura WordPress

1.&nbsp;Copia el archivo de configuración de muestra:

```bash
cd /var/www/html
sudo cp wp-config-sample.php wp-config.php
```

2.&nbsp;Edita el archivo `wp-config.php`:

```bash
sudo nano wp-config.php
```

Completa estos datos con la información de tu base de datos:

```php
define( 'DB_NAME', 'wordpress' );
define( 'DB_USER', 'wp_user' );
define( 'DB_PASSWORD', 'tu_contraseña' );
define( 'DB_HOST', 'localhost' );
```

---

### Paso 6: Configura el firewall (Opcional)

> **Nota:** Si deseas utilizar el comando `firewall-cmd`, ten en cuenta que no está disponible de serie en los paquetes de Amazon Linux 2023. Para instalarlo, utiliza el siguiente comando:

```bash
sudo yum install firewalld -y
```

Para comprobar la versión instalada:

```bash
sudo firewall-cmd --version
```

Permite el tráfico HTTP y HTTPS:

```bash
sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
sudo firewall-cmd --zone=public --add-port=443/tcp --permanent
sudo firewall-cmd --reload
```

---

### Paso 7: Reinicia Apache

Aplica todos los cambios:

```bash
sudo systemctl restart httpd
```

---

### Paso 8: Accede a WordPress

Abre tu navegador y dirígete a la IP pública de tu instancia EC2 o tu dominio:

```
http://tu-ip-publica
```

Sigue las instrucciones en pantalla para completar la instalación: selecciona idioma, crea una cuenta de administrador y configura tu sitio.

![Asistente de instalación 1](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/16a8s033c7xlmujaurxa.png)

![Asistente de instalación 2](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/soj86fgpagpq8a67c7i3.png)

![Asistente de instalación 3](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/69n5gnp4eugnmmbtzgtk.png)


![Asistente de instalación 4](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tlpqtt6qnpua5f238ycs.png)

---

## ¡Listo! 🎉

Ahora tienes WordPress corriendo en tu instancia EC2 con Amazon Linux 2023. ¿Tienes alguna duda o sugerencia? ¡Compártela en los comentarios! 👇🏻
