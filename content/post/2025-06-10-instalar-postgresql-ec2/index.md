---
title: Cómo instalar PostgreSQL en una instancia EC2 con Amazon Linux 2023
date: 2025-06-10T10:30:00+02:00
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

Si necesitas un servidor de base de datos robusto y confiable, **PostgreSQL** es una excelente opción.

En este tutorial, te guiaré paso a paso para instalar y configurar PostgreSQL en una instancia EC2 utilizando **Amazon Linux 2023**.

---

### Paso 1: Actualizar el sistema

Asegúrate de que tu sistema esté actualizado antes de instalar cualquier paquete. Para ello, ejecuta el siguiente comando:

```bash
sudo dnf update -y
```

---

### Paso 2: Instalar PostgreSQL

Amazon Linux 2023 incluye PostgreSQL 15 en su repositorio predeterminado, por lo que no necesitas agregar repositorios adicionales. Para instalar PostgreSQL (cliente y servidor), ejecuta:

```bash
sudo dnf install postgresql15.x86_64 postgresql15-server -y
```

> 💡 **Tip:** Si necesitas otra versión, verifica las disponibles con:

> ```bash
> sudo dnf list postgresql*
> ```

---

### Paso 3: Inicializar la base de datos

Inicializa el clúster de la base de datos PostgreSQL utilizando el siguiente comando:

```bash
sudo postgresql-setup --initdb
```

---

### Paso 4: Iniciar y habilitar el servicio de PostgreSQL

1.&nbsp;Inicia el servicio:

```bash
sudo systemctl start postgresql
```

2.&nbsp;Habilita el servicio para que inicie automáticamente al arrancar el sistema:

```bash
sudo systemctl enable postgresql
```

3.&nbsp;Verifica el estado del servicio:

```bash
sudo systemctl status postgresql
```

📸 Aquí tienes un ejemplo de cómo debería verse el estado del servicio:

![Estado del servicio PostgreSQL](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/21w84v097jsvqle20wyk.png)

---

### Paso 5: Configurar PostgreSQL

1.&nbsp;Establecer contraseñas para el usuario `postgres`

Cambia la contraseña del usuario de sistema y del administrador de la base de datos:

```bash
sudo passwd postgres
su - postgres
psql -c "ALTER USER postgres WITH PASSWORD 'your-password';"
exit
```

---

### ⚠️ Recomendaciones al configurar la contraseña

Cumple con los requisitos de seguridad:

- Al menos una letra mayúscula y una minúscula.
- Al menos un número.
- Al menos un símbolo especial (.!@#$%^&*()).
- Longitud mínima de 8 caracteres.

---

2.&nbsp;Configurar `postgresql.conf`

Crea un respaldo del archivo de configuración principal y luego edítalo:

```bash
sudo cp /var/lib/pgsql/data/postgresql.conf /var/lib/pgsql/data/postgresql.conf.bck
sudo vi /var/lib/pgsql/data/postgresql.conf
```

Si necesitas que PostgreSQL escuche en todas las direcciones IP, cambia:

```plaintext
listen_addresses = '*'
```

3.&nbsp;Configurar autenticación en `pg_hba.conf`

Crea un respaldo del archivo de autenticación y edítalo:

```bash
sudo cp /var/lib/pgsql/data/pg_hba.conf /var/lib/pgsql/data/pg_hba.conf.bck
sudo vi /var/lib/pgsql/data/pg_hba.conf
```

Cambia `ident` a `md5` para permitir conexiones con autenticación por contraseña:

```plaintext
host    all    all    0.0.0.0/0    md5
```

4.&nbsp;Reinicia el servicio para aplicar los cambios:

```bash
sudo systemctl restart postgresql
```

5.&nbsp;Crear un usuario y base de datos

Conéctate al servidor PostgreSQL como usuario `postgres` y ejecuta los siguientes comandos:

```bash
sudo -i -u postgres psql
CREATE USER yourusername WITH PASSWORD 'password';
CREATE DATABASE database_name;
GRANT ALL PRIVILEGES ON DATABASE database_name TO yourusername;
exit
```

Para listar las bases de datos disponibles:

```plaintext
\l
```

---

### Paso 6: Acceso a la base de datos

Puedes conectarte a PostgreSQL localmente con:

```bash
psql -h localhost -U username -d database_name
```

Para conexiones remotas, asegúrate de que el puerto **5432** esté permitido en el grupo de seguridad de tu instancia EC2.

---

## ⚠️ Configuraciones importantes de PostgreSQL

Como recordatorio, estas son algunas configuraciones clave de PostgreSQL que debes conocer:

| **Elemento de configuración**              | **Valor**                     |
|--------------------------------------------|--------------------------------|
| Puerto predeterminado de PostgreSQL        | 5432                           |
| Usuario predeterminado                     | postgres                       |
| Ubicación del archivo `postgresql.conf`    | `/var/lib/pgsql/data`          |
| Ubicación del archivo `pg_hba.conf`        | `/var/lib/pgsql/data`          |
| Base de datos predeterminada               | postgres                       |
| Directorio de datos predeterminado         | `/var/lib/pgsql/data`          |

---

## ¡Listo! 🎉

¡Eso es todo! Ahora tienes un servidor PostgreSQL configurado en tu instancia EC2 con Amazon Linux 2023.

