---
title: Configura un servidor GitLab en AWS EC2 en minutos
slug: "configurar-gitlab-ec2"
date: 2025-05-13T10:30:00+02:00
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

GitLab es una plataforma poderosa para la gestión de repositorios de código, CI/CD y colaboración en equipo.

En este tutorial, te mostraré cómo instalar y configurar GitLab en una instancia EC2 con Amazon Linux 2023. ¡Manos a la obra!

---

## Requisitos previos

Antes de empezar, asegúrate de cumplir con estos puntos:

1.&nbsp;Instancia EC2 configurada:

   - Asegúrate de que tu grupo de seguridad permita tráfico en los puertos **80 (HTTP)**, **443 (HTTPS)** y opcionalmente el **22 (SSH)**.
   - Si planeas usar un dominio o IP estática, asigna un **Elastic IP**.

2.&nbsp;Dominio (Opcional)

   - Configura un registro que apunte a la IP pública de tu instancia si tienes un dominio.

---

## Pasos para instalar GitLab en EC2

1.&nbsp;Actualiza tu sistema

Mantén tu sistema operativo al día:

```bash
sudo yum update -y
```

---

2.&nbsp;Instala las dependencias necesarias

GitLab requiere ciertos paquetes para funcionar correctamente:

```bash
sudo yum install -y curl policycoreutils-python-utils openssh-server
```

Si encuentras un error relacionado con paquetes en conflicto, usa este comando:

```bash
sudo yum install -y curl policycoreutils-python-utils openssh-server --allowerasing
```

---

3.&nbsp;Configura el servicio SSH

Habilita y arranca el servicio SSH:

```bash
sudo systemctl enable sshd
sudo systemctl start sshd
```

---

4.&nbsp;Instala GitLab

4.1.&nbsp;Agrega el repositorio oficial de GitLab

Usa este comando para configurar el repositorio:

```bash
curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
```

4.2.&nbsp;Instala GitLab Community Edition (CE)

Instala GitLab y especifica la URL externa (IP o dominio):

```bash
sudo EXTERNAL_URL="http://<tu-ip-o-dominio>" yum install -y gitlab-ce
```
Reemplaza `<tu-ip-o-dominio>` con la IP pública de tu instancia o tu dominio.

---

5.&nbsp;Configura GitLab

Ejecuta el script de configuración inicial para aplicar los ajustes:

```bash
sudo gitlab-ctl reconfigure
```

---

6.&nbsp;Accede a GitLab

6.1.&nbsp;Obtén la contraseña inicial de root

Después de la instalación, GitLab genera una contraseña para el usuario `root`. Encuéntrala con:

```bash
sudo cat /etc/gitlab/initial_root_password
```

El resultado será algo como:

```plaintext
# WARNING: This value is valid only in the initial login.
Password: A1b2C3d4E5f6G7h8
```

![Ejemplo contraseña root inicial](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2vyj7m4h1xumyozfp5jt.png)

6.2.&nbsp;Ingresa al navegador

Abre tu navegador y accede a `http://<tu-ip-o-dominio>`. Usa estas credenciales:
- **Usuario**: `root`
- **Contraseña**: La generada en el archivo anterior.

GitLab te pedirá cambiar esta contraseña en el primer inicio de sesión.

> **Nota**: Si el archivo `/etc/gitlab/initial_root_password` ya no existe, puede haberse eliminado automáticamente después de 24 horas. Si olvidaste la contraseña, puedes restablecerla manualmente.

![Pantalla inicial GitLab](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ogkddch1e393uz477cvk.png)

![Pantalla inicial administrador GitLab](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/c1ag4wux76a20xjlds88.png)

---

### **Opcional: Configura HTTPS con Let's Encrypt**

### Edita la configuración de GitLab

1.&nbsp;Abre el archivo de configuración:

```bash
sudo nano /etc/gitlab/gitlab.rb
```

2.&nbsp;Cambia la URL externa para usar HTTPS y habilita Let's Encrypt:

```ruby
external_url "https://<tu-dominio>"
letsencrypt['enable'] = true
```

3.&nbsp;Guarda los cambios y reconfigura GitLab:

```bash
sudo gitlab-ctl reconfigure
```

### Reinicia GitLab

Reinicia el servicio para asegurarte de que los cambios se apliquen correctamente:

```bash
sudo gitlab-ctl restart
```

---

## Notas finales

- **Firewall y conectividad**: Revisa los grupos de seguridad de tu instancia y el firewall local para asegurarte de que los puertos necesarios están abiertos.
- **Documentación oficial**: Consulta la [documentación de GitLab](https://docs.gitlab.com/ee/install/) para configuraciones avanzadas.
- **Dominio**: Usar un dominio con HTTPS mejora la seguridad y profesionalismo de tu plataforma GitLab.

¿Tienes dudas o comentarios? ¡Déjalos aquí y estaré encantado de ayudarte a resolverlos! ☺️👇🏻
