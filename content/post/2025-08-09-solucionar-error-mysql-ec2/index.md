---
title: ¿Cómo solucionar el error 'GPG key is already installed' al instalar MySQL en Amazon Linux 2023?
slug: "solucionar-error-gpg-mysql-ec2"
date: 2025-08-09T10:30:00+02:00
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

Si estás intentando instalar MySQL en una instancia EC2 con **Amazon Linux 2023**, probablemente te encuentres con este molesto error relacionado con las claves GPG:

```bash
The GPG keys listed for the "MySQL 8.0 Community Server" repository are already installed but they are not correct for this package.
```

Esto sucede porque MySQL actualizó sus claves GPG recientemente y algunas versiones del repositorio intentan utilizar claves obsoletas. Aquí tienes cómo resolver este problema y por qué ocurre.

---

### **¿Qué causa este error?**

Las claves GPG son utilizadas para verificar la integridad y autenticidad de los paquetes descargados. Sin embargo, la clave antigua `RPM-GPG-KEY-mysql-2022` expiró el **14 de diciembre de 2023**, y los paquetes más recientes (a partir de MySQL 8.0.36) ahora requieren la nueva clave `RPM-GPG-KEY-mysql-2023`

Por esta razón, aunque el repositorio configure automáticamente la clave anterior, falla al verificar los paquetes más recientes.

---

### **Cómo resolver el error**

Sigue estos pasos para importar la nueva clave GPG y completar la instalación de MySQL.

**1. Eliminar claves GPG obsoletas**

   Asegúrate de eliminar cualquier clave antigua que pueda estar causando conflictos.

   ```bash
   sudo rpm -e gpg-pubkey-3a79bd29
   ```

   Nota: `3a79bd29` es la clave antigua. Si ves otra clave conflictiva en los mensajes de error, reemplázala por el identificador correspondiente.

**2. Importar la nueva clave GPG de 2023**

   Descarga e importa manualmente la nueva clave GPG:

   ```bash
   sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
   ```

**3. Reintentar la instalación de MySQL**

   Ahora puedes volver a instalar MySQL sin problemas:

   ```bash
   sudo yum install mysql-community-server -y
   ```

---

### **Verifica las claves activas**

Para confirmar que las claves correctas están instaladas, utiliza este comando:

```bash
rpm -qa gpg-pubkey
```

Deberías ver algo como:

```bash
gpg-pubkey-a8d3785c-<timestamp>
```

El identificador `a8d3785c` corresponde a la nueva clave GPG válida para los paquetes MySQL recientes.

---

### **Un vistazo al futuro**

Según el [informe de errores de MySQL](https://bugs.mysql.com/bug.php?id=106188), estas claves se actualizan periódicamente. Es buena práctica:

- Consultar siempre las notas de lanzamiento o la documentación oficial de MySQL.
- Verificar la vigencia de las claves GPG antes de instalar o actualizar paquetes.

---

## Conclusión

Este problema puede parecer frustrante, pero ahora que sabes cómo solucionarlo, podrás instalar MySQL sin contratiempos en tu instancia EC2 con Amazon Linux 2023.

Si tienes alguna duda o te encuentras con otro error, ¡déjalo en los comentarios! Estoy aquí para ayudarte. 😊
