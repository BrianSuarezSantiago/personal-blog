---
title: Configura un repositorio Nexus con Docker y AWS EC2 en minutos
date: 2025-10-07T10:30:00+02:00
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

¿Quieres un repositorio central para manejar dependencias y artefactos en tus proyectos? ¡Sonatype Nexus es la solución! En este tutorial, aprenderás a instalar y configurar un Nexus Repository usando Docker en una instancia de AWS EC2.

---

### Requisitos previos

Antes de comenzar, asegúrate de tener lo siguiente:

- **Docker** instalado en tu instancia de EC2. [Revisa mi artículo sobre cómo instalar Docker en una instancia EC2.](https://dev.to/briansuarezsantiago/aprende-a-instalar-docker-en-una-instancia-ec2-con-amazon-linux-2023-y-ubuntu-3ncn)
- Conocer los **requisitos del sistema** de Nexus (revisa la [documentación oficial](https://help.sonatype.com/en/sonatype-nexus-repository-system-requirements.html)).

---

### Paso 1: Crear un volumen persistente

Para evitar perder datos si el contenedor se detiene, primero crearemos un volumen en Docker:

```bash
docker volume create --name nexus-data
```

Después de crear el volumen, puedes listar los volúmenes disponibles para asegurarte de que se haya creado correctamente:

```bash
docker volume ls
```

---

### Paso 2: Ejecutar el contenedor de Nexus

Usaremos la imagen oficial de Nexus para lanzar el servicio:

```bash
docker run -d -p 8081:8081 --name nexus -v nexus-data:/nexus-data sonatype/nexus3
```

- **Verifica que la imagen de Nexus se haya descargado correctamente:**

```bash
docker images | grep nexus3
```

- **Si el contenedor no aparece en ejecución al usar el comando `docker ps`, ejecútalo manualmente:**

```bash
docker start nexus
```

Por defecto, Nexus Repository Manager estará disponible en el puerto **8081**.

---

### Paso 3: Esperar la inicialización

Dale uno o dos minutos para que el contenedor se inicie completamente.

- **Prueba rápida:** Abre tu navegador y visita `http://<IP_de_tu_EC2>:8081`. Deberías ver la interfaz de Nexus Repository.

![Primera vista inicial de Nexus Repository Manager](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/30zp7bavj8fu0pidrdcw.png)

- **Prueba alternativa con cURL:**

```bash
curl http://<IP_de_tu_EC2>:8081
```

---

### Paso 4: Obtener la contraseña del administrador

Nexus genera una contraseña de administrador única la primera vez que se inicia. Para obtenerla:

Usa el siguiente comando para leer el archivo `admin.password` dentro del contenedor:

```bash
docker container exec nexus cat /nexus-data/admin.password
```

El resultado será algo como:

```
90ae9815-05c3-43f1-9ad0-cb83a1d6780e
```

![Ejemplo contraseña de administrador inicial](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lt2h4iv6yr40vz3wdhip.png)

---

### Paso 5: Inicia sesión en el panel de administración

1. Ve a `http://<IP_de_tu_EC2>:8081`
2. Inicia sesión con:
   - Usuario: `admin`
   - Contraseña: (la que obtuviste en el paso anterior)

![Pantalla inicial de inicio de sesión](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zc3ub9ab6r7eoqwszf6h.png)

---

### Paso 6: Configurar Nexus según tus necesidades

Desde el panel de administración, puedes:
- Crear repositorios para Maven, NPM, Docker, y más.
- Configurar permisos y usuarios.
- Integrar Nexus con tus pipelines de CI/CD.

---

### 🔑 Tips importantes para la configuración inicial

**1. Cambio obligatorio de contraseña**

   Al iniciar sesión por primera vez como administrador, Nexus te solicitará que cambies la contraseña inicial. Asegúrate de que la nueva contraseña cumpla con los siguientes requisitos:
   - Al menos una letra mayúscula y una minúscula.
   - Al menos un número.
   - Al menos un símbolo especial (.!@#$%^&*()).
   - Longitud mínima de 8 caracteres.

**2. Acceso anónimo**

   Durante la configuración inicial, Nexus te dará la opción de habilitar o deshabilitar el acceso anónimo:

   - **Habilitar acceso anónimo:** Los usuarios podrán buscar, navegar y descargar componentes de los repositorios sin necesidad de credenciales. Esto puede ser útil para proyectos públicos, pero debes evaluar los riesgos para tu organización.
   - **Deshabilitar acceso anónimo:** Requiere credenciales para todos los usuarios y herramientas de compilación, lo que es más seguro pero puede ser menos conveniente.

   > **Recomendación:** Si estás configurando Nexus para un entorno corporativo, considera deshabilitar el acceso anónimo para mantener un control más estricto sobre quién puede acceder a tus repositorios.

---

## ¿Listo para potenciar tus proyectos? 🚀

Ahora tienes un Nexus Repository completamente funcional corriendo en tu instancia EC2.

¿Te quedaron dudas? ¿O quizás ya tienes experiencia usando Nexus? ¡Comparte tus preguntas o experiencias en los comentarios! 👇🏻
