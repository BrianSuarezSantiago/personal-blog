---
title: Cómo instalar MariaDB en una instancia EC2 con Amazon Linux 2023
slug: "instalar-mariadb-ec2"
date: 2025-04-25T10:30:00+02:00
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

Si necesitas un servidor de base de datos rápido, confiable y fácil de configurar, **MariaDB** es una excelente opción. En este tutorial, te guiaré paso a paso para instalar y configurar MariaDB en una instancia EC2 con **Amazon Linux 2023**.

---

### Paso 1: Actualizar el sistema

Antes de instalar cualquier paquete, asegúrate de que tu sistema está actualizado:

```bash
sudo dnf update -y
```

---

### Paso 2: Instalar MariaDB

Amazon Linux 2023 incluye MariaDB en sus repositorios predeterminados.

1.&nbsp;Instala MariaDB Server

```bash
sudo dnf install -y mariadb105-server
```

> 💡 **Tip:** Si necesitas otra versión, verifica las disponibles con:

> ```bash
> sudo dnf list mariadb*
> ```

2.&nbsp;Inicia y habilita el servicio

Después de instalar, inicia el servicio de MariaDB:

```bash
sudo systemctl start mariadb
```

Y habilítalo para que inicie automáticamente tras un reinicio:

```bash
sudo systemctl enable mariadb
```

Verifica que el servicio esté funcionando:

```bash
sudo systemctl status mariadb
```

📸 Aquí tienes un ejemplo de cómo debería verse el estado del servicio:

![Estado MariaDB](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3hmzigbmn5x9vtm5tljd.png)

---

### Paso 3: Configurar MariaDB

#### Configuración de seguridad inicial

Antes de usar MariaDB, necesitas realizar una configuración inicial. A continuación, ejecuta el script de configuración inicial:

```bash
sudo mysql_secure_installation
```

---

### ⚠️ Recomendaciones al configurar la contraseña

**Cumple con los requisitos de seguridad:**
   - Al menos una letra mayúscula y una minúscula.
   - Al menos un número.
   - Al menos un símbolo especial (.!@#$%^&*()).
   - Longitud mínima de 8 caracteres.

#### ¿Qué hace el script `mysql_secure_installation`?

Este script te guía para realizar ajustes clave en la configuración de seguridad:

- **Cambiar la contraseña de `root`:** Usa una contraseña fuerte siguiendo las recomendaciones anteriores.
- **Deshabilitar usuarios anónimos:** Esto evita accesos no autorizados.
- **Eliminar la base de datos de prueba:** Protege tu sistema eliminando datos predeterminados.
- **Restringir el acceso remoto:** Asegura que solo se pueda acceder desde la máquina local.

---

### Vista del asistente de configuración

📸 Aquí tienes capturas de pantalla de cómo se ve el asistente durante su ejecución:

![Paso 1 del asistente de configuración de MariaDB](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hjh1gc865bnkvjfcz4mf.png)

![Paso 2 del asistente de configuración de MariaDB](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/uf9gnkyyh1polznlod06.png)

---

### Paso 4: Conéctate a MariaDB como root

Ejecuta el siguiente comando para acceder al servidor de MariaDB como usuario `root`.

```bash
sudo mysql -u root -p
```

Ingresa la contraseña configurada anteriormente y accede al shell de MariaDB.

---

## ¡Listo! 🎉

¡Eso es todo! Ahora tienes un servidor MariaDB funcionando en tu instancia EC2 con Amazon Linux 2023

Si tienes preguntas o necesitas ayuda para personalizar tu configuración, ¡déjalo en los comentarios! 👇🏻
