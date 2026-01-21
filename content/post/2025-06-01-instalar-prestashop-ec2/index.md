---
title: Cómo instalar PrestaShop en una instancia EC2 con Amazon Linux 2023
slug: "instalar-prestashop-ec2"
date: 2025-06-01T10:30:00+02:00
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

Si quieres montar una tienda online en AWS, PrestaShop es una gran opción. En este post te explico paso a paso cómo instalarlo en una instancia EC2 con **Amazon Linux 2023**. ¡Vamos allá!

# Instalar Prestahop

1.&nbsp;Actualizar el sistema

Antes de instalar cualquier cosa, actualiza los paquetes existentes:

```bash
sudo yum update -y
```

2.&nbsp;Instalar Apache

PrestaShop necesita un servidor web, así que instalamos **Apache**:

```bash
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd
```

Verifica que está corriendo:

```bash
curl http://localhost
```

Si ves la estructura de una página HTML, significa que Apache está funcionando correctamente.

3.&nbsp;Instalar PHP y extensiones necesarias

PrestaShop requiere PHP. Instalamos la versión recomendada:

```bash
sudo amazon-linux-extras enable php8.2
sudo yum install -y php php-mysqlnd php-curl php-dom php-mbstring php-gd php-opcache php-zip
```

Comprueba la instalación:

```bash
php -v
```

4.&nbsp;Instalar MariaDB (o MySQL) y configurar la base de datos

PrestaShop necesita una base de datos, así que instalamos **MariaDB**:

```bash
sudo yum install -y mariadb105-server
sudo systemctl enable mariadb
sudo systemctl start mariadb
```

5.&nbsp;Ejecutamos la configuración inicial:

```bash
sudo mysql_secure_installation
```

6.&nbsp;Ahora, creamos la base de datos y un usuario:

```bash
mysql -u root -p
```

Dentro de la consola de MySQL, ejecuta:

```sql
CREATE DATABASE prestashop;
CREATE USER 'prestashopuser'@'localhost' IDENTIFIED BY 'tu_contraseña_segura';
GRANT ALL PRIVILEGES ON prestashop.* TO 'prestashopuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

7.&nbsp;Descargar la última versión de PrestaShop

Descarga la última versión desde el repositorio oficial de GitHub:

```bash
wget https://github.com/PrestaShop/PrestaShop/releases/download/8.2.0/prestashop_8.2.0.zip -O prestashop.zip
```

> Puedes acceder a otras versiones de Prestashop a través del repositorio oficial en GitHub.
> https://github.com/PrestaShop/PrestaShop/releases

8.&nbsp;Instala la herramienta `unzip` y extrae los archivos:

```bash
sudo yum install -y unzip
unzip prestashop.zip -d /var/www/html/prestashop
```

9.&nbsp;Ajusta los permisos:

```bash
sudo chown -R apache:apache /var/www/html/prestashop
sudo chmod -R 755 /var/www/html/prestashop
```

# Configurar Apache para PrestaShop

1.&nbsp;Crea un archivo de configuración para PrestaShop:

```bash
sudo nano /etc/httpd/conf.d/prestashop.conf
```

Agrega lo siguiente:

```apache
<VirtualHost *:80>
    ServerAdmin admin@tudominio.com
    DocumentRoot /var/www/html/prestashop
    ServerName tudominio.com
    ServerAlias www.tudominio.com

    <Directory /var/www/html/prestashop>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog /var/log/httpd/prestashop-error.log
    CustomLog /var/log/httpd/prestashop-access.log combined
</VirtualHost>
```

2.&nbsp;Reinicia Apache:

```bash
sudo systemctl restart httpd
```

Verifica que está funcionando:

```bash
sudo systemctl status httpd
```

# Completar la instalación desde el navegador

Ahora, abre tu navegador y accede a:

```plaintext
http://direccion-ip-ec2
```

- Sigue el asistente de instalación de PrestaShop.
- Introduce los datos de la base de datos que creaste anteriormente.
- Configura las credenciales de administrador.

⚠️ **Importante:** Una vez completada la instalación, **elimina la carpeta de instalación** por seguridad:

```bash
sudo rm -rf /var/www/html/prestashop/install
```

![Requisito eliminación directorio instalación](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/384jwz8rd7f9zzzwg2va.png)

📸 Aquí tienes algunos ejemplos del proceso de instalación:

![Proceso de instalación 1](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/g7kj6e0vwba1j96hl5l2.png)

![Proceso de instalación 2](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/nsjqlyvyvm9c9idn2o5x.png)

![Proceso de instalación 3](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cokpfrp6csl4ouugxcw8.png)

![Proceso de instalación 4](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/q95eujbt4gdhzs6pi9ph.png)

![Proceso de instalación 5](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/d3gxw6tonik0ca9ch6m6.png)

![Proceso de instalación 6](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/c6r70sltv360egbs6l05.png)

---

# ¡Tu tienda online está lista!

Con estos pasos, ya tienes PrestaShop funcionando en AWS con **Amazon Linux 2023**. Ahora puedes empezar a personalizar tu tienda, agregar productos y lanzar tu negocio online.

Si tienes dudas o comentarios, ¡déjamelos aquí abajo! 👇🏻
