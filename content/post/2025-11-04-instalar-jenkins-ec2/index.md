---
title: Cómo instalar Jenkins en AWS
date: 2025-11-04T10:30:00+02:00
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

Jenkins es un servidor de automatización de código abierto que se integra fácilmente con servicios de AWS como CodeCommit, CodeDeploy y Amazon EC2. Esta guía te muestra cómo instalar Jenkins en una instancia de Amazon EC2 y configurarlo para que puedas potenciar tus proyectos sin complicaciones.

---

# Instalar y configurar Jenkins

1.&nbsp;Actualiza los paquetes:

```bash
sudo yum update -y
```

2.&nbsp;Agrega el repositorio de Jenkins:

```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo

sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
   ```

3.&nbsp;Instala Java y Jenkins:

```bash
sudo dnf install java-17-amazon-corretto -y
sudo yum install jenkins -y
```

4.&nbsp;Activa y arranca Jenkins:

```bash
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

5.&nbsp;Verifica que Jenkins esté funcionando:

```bash
sudo systemctl status jenkins
```

![Estado del servicio Jenkins](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/49t1auoypun45s6e1k4v.png)

### Configura Jenkins

1.&nbsp;Abre tu navegador y ve a: `http://<public_dns_name>:8080`.

2.&nbsp;Ingresa la contraseña inicial ejecutando:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

![Contraseña de administrador inicial en Jenkins](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4ns1kiprof1lkihluq93.png)

3.&nbsp;Sigue el asistente para:
   - Instalar los plugins sugeridos.

   - Crear tu primer usuario administrador.

![Asistente de instalación 1](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ldry3l2fb9w5u30zq1l7.png)

![Asistente de instalación 2](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/j8p6jtp1hizblgc9loge.png)

![Asistente de instalación 3](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vvmyfwaygmjrgffswyea.png)

![Asistente de instalación 4](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/932xo2qgd3788jmuc7ly.png)

![Asistente de instalación 5](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fix0czckbq2f5l56sz41.png)

![Asistente de instalación 6](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3j067chqm96ecirgnrdk.png)

![Asistente de instalación 7](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1rzuec5fmwu3t2dcj1an.png)

![Servidor Jenkins en ejecución](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kc5z14jsvzecwbk1mvhl.png)

---

# ¡Y listo!

Ahora tienes Jenkins configurado en AWS, listo para automatizar tus tareas y proyectos. Si tienes dudas o comentarios, ¡déjamelos aquí abajo! 👇🏻
