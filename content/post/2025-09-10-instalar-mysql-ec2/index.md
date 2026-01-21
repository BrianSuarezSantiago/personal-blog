---
title: Cómo instalar MySQL en una instancia EC2 con Amazon Linux 2023
slug: "instalar-mysql-ec2"
date: 2025-09-10T10:30:00+02:00
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

¿Estás trabajando con una instancia EC2 usando Amazon Linux 2023 y necesitas MySQL? A diferencia de versiones anteriores, MySQL ya no viene preinstalado, y el proceso de instalación puede ser algo confuso. ¡Pero no te preocupes! Aquí tienes una guía paso a paso para instalarlo sin complicaciones y configurarlo adecuadamente.

---

### **1. Descarga el archivo RPM de MySQL**

El primer paso es descargar el archivo del repositorio de MySQL. Esto te permitirá acceder a las versiones más recientes:

```bash
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
```

---

### **2. Instala el archivo RPM**

Este archivo configurará el repositorio necesario para la instalación:

```bash
sudo dnf install mysql80-community-release-el9-1.noarch.rpm -y
```

---

### **3. Importa la clave pública de MySQL**

Para evitar problemas con la autenticación de los paquetes, importa la clave GPG de MySQL:

```bash
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
```

---

### **4. Instala MySQL según tus necesidades**

#### **Solo cliente MySQL:**

```bash
sudo dnf install mysql-community-client -y
```

#### **Servidor MySQL (incluye cliente):**

```bash
sudo dnf install mysql-community-server -y
```

---

### **5. Configura el servidor MySQL**

Inicia el servicio y asegúrate de que arranque automáticamente con el sistema:

```bash
sudo systemctl start mysqld
sudo systemctl enable mysqld
```

Verifica que el servicio esté funcionando:

```bash
sudo systemctl status mysqld
```

📸 Aquí tienes un ejemplo de cómo debería verse el estado del servicio:

![Estado de MySQL](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kwg5sl793jqk9krjg4x3.png)

---

### **6. Configuración inicial de MySQL**

Antes de usar MySQL, necesitas realizar una configuración inicial. Primero, encuentra la contraseña temporal generada durante la instalación:

```bash
sudo grep 'temporary password' /var/log/mysqld.log
```

🔑 Un ejemplo del resultado se muestra a continuación:

![Contraseña temporal](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ekb7g531mgoppuivsopw.png)

A continuación, ejecuta el script de configuración inicial:

```bash
sudo mysql_secure_installation
```

---

### **Recomendaciones al configurar la contraseña**

**1. Cumple con los requisitos de seguridad:**
   - Al menos una letra mayúscula y una minúscula.
   - Al menos un número.
   - Al menos un símbolo especial (.!@#$%^&*()).
   - Longitud mínima de 8 caracteres.

Si no cumples con estos criterios, verás un error como este:

   > `ERROR 1819 (HY000): Your password does not satisfy the current policy requirements.`

![Error de contraseña](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/mey9mnwbeip843ky7spp.png)

**2. Evalúa el nivel de seguridad:**

MySQL te mostrará un porcentaje que indica qué tan segura es tu contraseña. Un nivel del 100% indica la máxima seguridad.

![Nivel de seguridad](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/858innrpkmo4wwc23vo5.png)

**3. Ajusta la política de validación (opcional):**

Si prefieres contraseñas menos estrictas, puedes modificar la política de validación en el archivo `/etc/my.cnf` o mediante comandos dentro de MySQL.

---

### **¿Qué hace el script `mysql_secure_installation`?**

Este script te guía para realizar ajustes clave en la configuración de seguridad:

- **Cambiar la contraseña de `root`:** Usa una contraseña fuerte siguiendo las recomendaciones anteriores.
- **Deshabilitar usuarios anónimos:** Esto evita accesos no autorizados.
- **Eliminar la base de datos de prueba:** Protege tu sistema eliminando datos predeterminados.
- **Restringir el acceso remoto:** Asegura que solo se pueda acceder desde la máquina local.

---

### **Vista del asistente de configuración**

📸 Aquí tienes capturas de pantalla de cómo se ve el asistente durante su ejecución:

![Paso 1 del asistente](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xj52nghe6ox6xb2kd5k2.png)

![Paso 2 del asistente](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7yzgw279no58y2g3qx12.png)

---

## ¡Listo! 🎉

Ahora tienes MySQL completamente instalado y configurado en tu instancia EC2 con Amazon Linux 2023.

¿Tienes alguna duda o quieres compartir tu experiencia? ¡Déjalo en los comentarios! 👇🏻
