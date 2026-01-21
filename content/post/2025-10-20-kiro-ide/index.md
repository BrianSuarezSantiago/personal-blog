---
title: "Kiro: El nuevo IDE de AWS que revoluciona la programación"
date: 2025-10-20T17:30:00+02:00
image: https://d1hvrz5kjt6fwi.cloudfront.net/Blog%20Posts%20Covers/Post%2001/Post%2001_horizontal_es.png
draft: false
description: ¿Revolución o marketing? La apuesta de AWS para que la IA escriba software sostenible (y no solo rápido)
tags:
  - AWS
  - Kiro
  - AI
categories:
  - AWS
  - AWS Developer Tools
  - AI
---

Hace poco más de un mes salí de la lista de espera de Kiro, el nuevo entorno de desarrollo que AWS lanzó como respuesta a Windsurf y Cursor. En teoría, parecía otro IDE más con IA integrada. En la práctica, me encontré con algo mucho más distinto de lo que esperaba.

Cuando lo anunciaron en julio, muchos desarrolladores se lanzaron a probarlo, tantos que el acceso se cerró por saturación. Ahora que lo he usado a fondo, puedo decir que Kiro no intenta competir de frente con Windsurf o Cursor, sino que propone una idea un poco más ambiciosa: dejar atrás el “vibe coding” y centrarse en algo que ellos llaman desarrollo basado en especificaciones.

Aparentemente, hay al menos seis IDE importantes basados en bifurcaciones de Visual Studio Code; conozco Cursor, Windsurf, y ahora Kiro.

En este post, te cuento más sobre este último.

¿Qué es exactamente Kiro?

Según la documentación oficial de AWS, Kiro es un entorno de desarrollo integrado con inteligencia artificial pensado para construir proyectos desde una idea inicial hasta una aplicación completamente funcional (al menos eso aseguran). Suena como marketing, pero la diferencia clave es el proceso: en lugar de escribir prompts al vuelo para que el modelo genere código, aquí se trabaja a partir de especificaciones detalladas, una especie de plano técnico que define lo que quieres construir, cómo debe funcionar y qué tareas implica.

A diferencia de las herramientas de los competidores, que dependen en gran medida de la generación rápida de código a partir de un prompt, Kiro adopta un enfoque más estructurado conocido como desarrollo basado en especificaciones. En pocas palabras, no le dices “hazme esto”, sino “esto es lo que necesito, y aquí están los detalles”. Y Kiro se encarga de transformar esas especificaciones en código, documentación y hasta pruebas automatizadas. AWS promete que el resultado es más limpio, mantenible y sobre todo entendible por humanos.

Fun fact: “Kiro” significa encrucijada en japonés (きろ), una metáfora perfecta para la idea de combinar lo mejor del desarrollo de software tradicional con la aceleración impulsada por la IA.

La herramienta es compatible con los sistemas operativos Windows, macOS y Linux, y funciona con los lenguajes de programación más populares. Está basado en Code OSS (el núcleo de código abierto de Visual Studio Code), por lo que en su esencia es un fork de VSCode (como era de esperar) y está desarrollado y soportado íntegramente por AWS. No obstante, permite el acceso a él sin disponer de una cuenta.

Actualmente, se encuentra gratuito durante su fase de prueba, lo que supone una gran oportunidad para probarlo sin comprometerse a una suscripción (así que te animo a que le des una oportunidad).

¿Cómo instalarlo?

Al momento de escribir este post el acceso a Kiro ha sido liberado y por tanto es accesible para cualquier persona sin necesidad de pasar a través de una waitlist.

La instalación es muy sencilla, basta con seguir la siguiente guía paso a paso y en pocos minutos tendrás Kiro instalado. Esta guía muestra el proceso de instalación y configuración en el sistema operativo macOS, pero los pasos son prácticamente iguales en Windows y Linux.

Accede al sitio web oficial de Kiro

Accede a la página web oficial de Kiro.

Descargar el instalador

Haz clic en el botón de descarga en función de tu sistema operativo. En mi caso he utilizado macOS, por lo que descargo el archivo ejecutable .dmg.

Una vez descargado, ejecuta el instalador y sigue los pasos del asistente de configuración para completar el proceso.

Registro en Kiro

Una vez completada la instalación, Kiro te pedirá iniciar sesión. Puedes elegir entre varias opciones de inicio de sesión.

Ventajas de utilizar AWS Builder ID

Al iniciar sesión con AWS Builder ID, se desbloquea la integración directa con Amazon Q dentro de Kiro. Si tienes acceso a la versión Amazon Q Developer o Amazon Q Pro, puedes mejorar significativamente la experiencia al integrar el asistente basado en IA de AWS con Kiro y obtener de esta manera un análisis de código más detallado, explicaciones y asistencia durante el desarrollo. No es obligatorio, pero mejora mucho la experiencia.

Importar la configuración de Visual Studio Code (Opcional)

Por último, Kiro ofrece la opción de importar los ajustes y configuraciones existentes de VSCode de manera que se mantengan los temas, extensiones y preferencias que tienes en este IDE. Para esta guía, omitiremos este paso y continuaremos con la configuración predeterminada. Si al igual que yo prefieres partir de cero, la configuración por defecto funciona sin problemas.

¿Qué tiene de novedoso?

Al abrirlo, el diseño es muy similar a Visual Studio Code, lo que ayuda a sentirse en casa desde el primer minuto. Sin embargo, Kiro añade una barra lateral con secciones que no existen en otros IDE. Al abrir el panel, por defecto muestra 4 elementos: Specs, Agent Hooks, Agent Steering y MCP Servers.

Specs

Las Specs son una forma estructurada de construir y documentar funcionalidades que quieres desarrollar con Kiro. Aquí defines las especificaciones de tu proyecto. Es el corazón del sistema. Puedes iterar con el agente sobre los requisitos, el diseño y las tareas de implementación.

Ejemplos de casos de uso

Iterar con el agente sobre requerimientos, diseño y tareas de implementación.

Desarrollar de manera incremental funcionalidades complejas con control y retroalimentación.

Documentar el proceso desde la concepción hasta la implementación.

Agent Hooks

Los Agent Hooks son la capacidad de Kiro para crear acciones automáticas del agente que se ejecutan al producirse ciertos eventos. Básicamente, automatizan acciones que normalmente harías tú a mano.

Ejemplos de hooks

Al guardar un archivo de código, se activa automáticamente una acción para actualizar y ejecutar tests unitarios.

Al modificar strings de traducción, se actualizan y se asegura de que otros idiomas también se actualicen.

Hook manual de spell-check, en el que al hacer clic en el botón de “spell check”, revisa y corrige errores gramaticales en el archivo README.

Agent Steering

Steering permite incluir contexto adicional e instrucciones que guían el comportamiento de las interacciones con Kiro según el archivo o la tarea en la que estés trabajando. Por defecto, se almacenan en .kiro/steering/*.md y pueden ser:

Tipos de inclusión

Siempre incluidos (comportamiento por defecto)

Condicionales cuando se lee un archivo específico (usando front-matter con inclusion fileMatch y fileMatchPattern: 'README*')

Manuales cuando los proporcionas vía contexto con '#' en el chat (configurado con inclusion manual)

MCP Servers

El Model Context Protocol (MCP) es un estándar que permite a aplicaciones de IA conectarse con herramientas y servicios externos, extendiendo significativamente sus capacidades más allá del desarrollo básico. En nuestro caso la aplicación de IA que queremos potenciar es Kiro y para ello, se pueden emplear servidores MCP para dotarle de capacidades de conexión con APIs, bases de datos o aplicaciones de terceros con el fin de integrar workflows complejos.

Ejemplos de uso de MCP Servers

Integración con GitHub
Conectar Kiro a un servidor MCP que gestione repositorios de GitHub y permita crear, revisar y fusionar pull requests automáticamente, sincronizar issues con las Specs del proyecto, y mantener el historial de cambios sin salir del IDE.

Conexión con bases de datos
A través de un servidor MCP que exponga acceso a bases de datos (por ejemplo, PostgreSQL o DynamoDB), Kiro puede ejecutar consultas, analizar datos o validar esquemas según las Specs, ayudando en tareas de backend, migraciones o verificaciones de integridad.

Automatización de servicios externos
Un MCP Server conectado a herramientas como Slack, Notion o Jira que permita a Kiro actualizar tareas, publicar reportes de progreso o notificar eventos del pipeline de desarrollo sin intervención manual, creando un flujo de trabajo más integrado y ágil.

AWS promete profundizar en esta última función en futuras actualizaciones, y por lo que he visto, hay mucho potencial.

En un futuro post mostraré el proceso de configuración de diferentes servidores MCP en Kiro y en Amazon Q CLI.

¿Por qué destaca Kiro?

En un mundo en el que las herramientas de programación con IA están surgiendo rápidamente, Kiro no intenta ser el más “creativo” ni el que genera más líneas de código por minuto. Su apuesta es la estructura.

En un panorama donde el “vibe coding” produce montañas de código difícil de mantener, Kiro propone una metodología más controlada y sostenible. Al hacer hincapié en las especificaciones y la automatización frente a las herramientas basadas en prompt únicamente, la filosofía detrás de Kiro pretende producir software listo para producción que no se desmorone cuando sea necesario escalarlo, depurarlo o simplemente, que no se convierta en una pesadilla mantenerlo en el tiempo.

En un futuro post mostraré un ejemplo completo de un caso de uso real de las features que ofrece Kiro.

Planes de pago

La facturación de Kiro se basa en créditos.

No obstante, actualmente tras el cierre de la fase beta, Kiro está en fase gratuita y, al registrarte, recibirás 500 créditos de bienvenida válidos durante 30 días.

Luego es posible continuar con el plan gratuito de 50 créditos mensuales o pasar a niveles de pago. AWS ofrece distintos niveles de precios flexibles diseñados en función del uso que los desarrolladores hacen de Kiro. Puedes pasar a niveles de pago que van desde Pro (20$ al mes), Pro+ (40$ al mes) hasta Power (200$ al mes) para aumentar la capacidad y cuota de uso de los modelos. Cada uno de los niveles de pago incluye la posibilidad de pagar los excedentes de uso de los LLM de manera flexible a 0,04$ por crédito adicional.

Cada acción que le pidas a Kiro que ejecute, desde generar especificaciones hasta ejecutar hooks consume créditos.

Es un modelo similar al de otras herramientas de IA, pero con la ventaja de poder probarlo a fondo antes de invertir (así que aprovecha la oportunidad).

En este enlace se encuentra una guía detallada de los modelos de suscripción existentes.

Novedades y cierre de la beta

Tras una beta masiva en la que más de 100.000 desarrolladores se lanzaron a probarlo en los primeros días, recientemente AWS ha liberado oficialmente Kiro para todos dejando atrás la fase beta.

Con esta versión llegan dos grandes mejoras: soporte para el modelo Claude Sonnet 4.5 de Antrophic y un nuevo agente llamado Auto, que selecciona automáticamente la mejor combinación de modelos según la tarea. En la práctica, esto hace que el sistema elija por ti si conviene usar un modelo más rápido, más barato o más preciso.

Reflexión final

Después de un mes usando Kiro, tengo la sensación de que AWS está intentando redefinir la programación asistida por IA. En lugar de perseguir la inmediatez del prompt, apuesta por una forma más reflexiva y estructurada de desarrollar.

No es una herramienta para quien solo quiere generar prototipos rápidos, sino para quienes buscan crear software serio, escalable y bien documentado. Kiro no elimina la necesidad de pensar, la reorganiza y te ayuda a hacerlo mejor.

En resumen, mientras otros IDEs se centran en “vibrar” con el código, Kiro intenta devolverle forma, orden y propósito. Y eso, sinceramente, se agradece.

---

Si este artículo te ha resultado útil o tienes alguna pregunta, puedes contactar conmigo en LinkedIn.
