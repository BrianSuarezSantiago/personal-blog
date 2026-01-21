---
title: Cómo instalar SonarQube en una instancia EC2 con Ubuntu
slug: "instalar-sonarqube-ec2"
date: 2026-01-04T10:30:00+02:00
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

Si estás buscando una herramienta potente para analizar la calidad del código y mejorar la gestión de tus proyectos, **SonarQube** es una opción fantástica.

En este tutorial, te guiaré paso a paso para instalarlo en una instancia EC2 con Ubuntu. ¡Vamos a ello!

---

### 1. Actualiza tu sistema

Lo primero es asegurarte de que tu sistema esté completamente actualizado. Ejecuta los siguientes comandos:

```bash
sudo apt update -y
sudo apt upgrade -y
```

Esto ayudará a evitar problemas derivados de paquetes desactualizados.

---

### 2. Instala Java

SonarQube necesita **Java 11** para funcionar. Instálalo con este comando:

```bash
sudo apt install openjdk-11-jre -y
```

Después, verifica que la instalación fue exitosa:

```bash
java --version
```

Deberías ver la versión de Java instalada confirmando que todo está en orden.

---

### 3. Crea un usuario para SonarQube

Por seguridad, es recomendable crear un usuario dedicado para SonarQube. Hazlo con:

```bash
sudo adduser sonarqube
```

Puedes completar la información solicitada (como nombre completo o teléfono) o simplemente presionar **Enter** para dejarlo vacío.

Luego, agrega al usuario `sonarqube` al grupo de administradores (`sudoers`) para que tenga permisos elevados cuando sea necesario:

```bash
sudo usermod -aG sudo sonarqube
```

Confirma que el usuario pertenece al grupo correcto:

```bash
groups sonarqube
```

---

### 4. Cambia al usuario SonarQube y configura su contraseña

Ahora, cambia al usuario recién creado con el siguiente comando:

```bash
su sonarqube
```

**Nota:** La primera vez que te conectas como este usuario, se te pedirá que configures una contraseña obligatoriamente. Si en el futuro deseas cambiarla nuevamente, puedes hacerlo ejecutando:

```bash
passwd
```

Luego, navega al directorio personal del usuario:

```bash
cd /home/sonarqube
```

---

### 5. Descarga e instala SonarQube

Primero, asegúrate de que la herramienta `zip` esté instalada, ya que la necesitarás para descomprimir los archivos de SonarQube. Instálala con:

```bash
sudo apt install zip -y
```

Descarga la última versión de SonarQube desde su sitio oficial. En este tutorial utilizaremos la versión **9.9.8.100196**:

```bash
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.8.100196.zip
```

Luego, descomprime el archivo descargado:

```bash
unzip sonarqube-9.9.8.100196.zip
```

Finalmente, establece los permisos correctos para garantizar el buen funcionamiento:

```bash
sudo chmod -R 755 /home/sonarqube/sonarqube-9.9.8.100196
sudo chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.9.8.100196
```

---

### 6. Inicia SonarQube

Dirígete al directorio donde se encuentra el script para iniciar SonarQube:

```bash
cd /home/sonarqube/sonarqube-9.9.8.100196/bin/linux-x86-64
```

Inicia SonarQube ejecutando:

```bash
./sonar.sh start
```

---

### 7. Verifica que el puerto 9000 esté activo

SonarQube utiliza el puerto **9000** de forma predeterminada. Asegúrate de que está escuchando:

1. Instala la herramienta `net-tools` si aún no lo has hecho:

   ```bash
   sudo apt install net-tools -y
   ```

2. Lista todos los puertos que están en escucha:

   ```bash
   sudo netstat -tulnp
   ```

3. Para verificar específicamente el puerto 9000, ejecuta:

   ```bash
   sudo netstat -tuln | grep :9000
   ```

Si todo está en orden, el puerto debería estar activo y listo para recibir conexiones.

---

### 8. Accede a SonarQube

Abre tu navegador y dirígete a la siguiente dirección:

```plaintext
http://<IP_de_tu_EC2>:9000
```

Reemplaza `<IP_de_tu_EC2>` con la dirección IP pública de tu instancia EC2. Si no estás seguro de cuál es, puedes encontrarla en la consola de AWS.

Cuando ingreses por primera vez, utiliza las credenciales predeterminadas:
- **Usuario**: `admin`
- **Contraseña**: `admin`

En el primer inicio de sesión, SonarQube te pedirá que cambies la contraseña.

---

## ¡Listo! 🎉

Ahora tienes SonarQube configurado y listo para usar en tu instancia EC2 con Ubuntu. Desde aquí, puedes comenzar a analizar tus proyectos, rastrear problemas de calidad y mejorar tu código de manera continua.

¿Tienes dudas o comentarios? Comparte tus experiencias o cualquier problema que hayas enfrentado durante la instalación. Estoy aquí para ayudarte, y tu experiencia podría ayudar a otros también.

¡Te leo en los comentarios! 👇🏻
