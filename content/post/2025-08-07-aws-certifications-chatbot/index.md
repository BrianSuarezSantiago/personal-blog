---
title: Crea tu coach personal para las certificaciones de AWS con Claude y Amazon Bedrock
date: 2025-08-07T17:30:00+02:00
image: https://d1hvrz5kjt6fwi.cloudfront.net/Blog%20Posts%20Covers/Post%2002/Post%2002_horizontal_es.png
draft: false
description: Tu nuevo coach de AWS no duerme, no se cansa y lo sabe todo
tags:
  - AWS
  - Level 100
categories:
  - AWS
  - AWS Workshops
  - AI
---

Prepararse para una certificación de AWS se parece mucho a entrenar para una maratón; es emocionante, desafiante y, en ocasiones, un poco abrumador. Entre el trabajo, la vida personal y la enorme cantidad de material de estudio disponible, mantener la constancia y la claridad puede convertirse en todo un reto. Ahora bien, ¿imaginas contar con un entrenador disponible las 24 horas del día, los 7 días de la semana, que te guíe paso a paso, te plantee preguntas cuando lo necesites y te ayude a estudiar de forma más inteligente?

En esta guía exploraremos cómo usar la IA generativa a través de los modelos Claude de Anthropic con Amazon Bedrock, el servicio de GenAI totalmente gestionado de AWS con el que podrás crear tu propio entrenador personal de certificación. Este asistente puede ayudarte a reforzar conceptos clave, simplificar ideas complejas y mantener el ritmo hacia tus objetivos de aprendizaje.

Al final de este artículo, comprenderás cómo aprovechar el poder de Amazon Bedrock y Claude Sonnet 4 para crear una experiencia de estudio personalizada que se adapte a tu estilo, ritmo de aprendizaje y tus objetivos de certificación.

Así que, si estás list@ para mejorar tu preparación para las certificaciones de AWS con técnicas de estudio basadas en IA, esta guía será tu mejor aliada para transformar por completo tu enfoque de aprendizaje.

Comprender cómo la IA puede ser tu aliada de estudio

Antes de configurar nada, conviene entender el papel que puede desempeñar la IA en tu proceso de preparación. Los modelos de lenguaje grandes (LLM), como los de Anthropic, pueden actuar como compañeros de estudio siempre disponibles. Piensa en ellos como un compañero de estudio incansable, dispuesto a ayudarte cuando algo se te atasca.

Amazon Bedrock ofrece acceso a una amplia gama de modelos de IA desarrollados por empresas líderes del sector como Anthropic, AI21 Labs, Amazon, Cohere, Meta, Mistral AI, OpenAI o Stability AI. Estos modelos son capaces de adaptar sus respuestas a tus necesidades de aprendizaje, desglosar temas complejos en partes más comprensibles y proporcionar retroalimentación inmediata sobre tus preguntas. Lo mejor de todos, es un servicio completamente gestionado por AWS, por lo que, su uso es muy sencillo.

La gran ventaja es la flexibilidad: puedes estudiar temprano en la mañana, tarde por la noche o en cualquier momento que se adapte a tus necesidades. Tu asistente de IA estará siempre disponible para mantenerte enfocado y ayudarte a progresar a tu ritmo.

Requisitos previos

Antes de empezar, asegúrate de contar con los permisos adecuados en Identity and Access Management (IAM) que permita el acceso al modelo en cuestión. En la documentación oficial encontrarás ejemplos de políticas basadas en identidades para Amazon Bedrock.

Recientemente, AWS ha eliminado la necesidad de habilitar los modelos fundacionales (FM) así que, ahora puedes hacer uso de ellos sin tener que activarlos previamente. Recuerda que el servicio funciona bajo un esquema de pago por uso (pay as you go) por lo que, solo pagarás por el tiempo o los recursos que utilices. Puedes monitorear tus costos fácilmente desde la consola de administración de AWS, lo que te ayudará a controlar y gestionar los costes de forma eficaz.

En este enlace puedes consultar los precios de Amazon Bedrock para obtener más información sobre los costes específicos de cada modelo básico.

Configuración del entorno de estudio con IA

Para configurar el entorno de estudio, siga estos pasos:

1. Habilitar el acceso de uso al modelo
Los usuarios tienen que tener permisos IAM para acceder al modelo Claude Sonnet 4 (o el LLM elegido) desde la consola de Amazon Bedrock.

{
    "Version":"2012-10-17",
    "Statement": [
        {
            "Sid": "ProvisionedThroughputModelInvocation",
            "Effect": "Allow",
            "Action": [
                "bedrock:InvokeModel",
                "bedrock:InvokeModelWithResponseStream"
            ],
            "Resource": "arn:aws:bedrock:eu-west-1:${aws:username}:provisioned-model/${aws:modelId}"
        }
    ]
}

2. Seleccionar el modelo adecuado
En el entorno de pruebas Amazon Bedrock Chat/Text, selecciona Claude Sonnet 4 entre los modelos base disponibles.

3. Definir un mensaje del sistema (System prompt)
Este mensaje establece el contexto inicial del asistente, es decir, su rol, tono y enfoque. Por ejemplo:

“You are an expert AWS Certification Coach with deep knowledge of AWS services and certification exams. You specialize in the AWS Solutions Architect Associate certification. Use the documentation uploaded to understand the exam you are generating questions for. Always base your answers on official AWS documentation and best practices. Keep explanations clear and focused on exam-relevant details. If unsure about any information, acknowledge the uncertainty and refer to official AWS documentation.”

Si lo prefieres en español:

“Eres un experto entrenador en certificaciones de AWS, especializado en el examen de AWS Solutions Architect Associate. Basa tus respuestas en la documentación oficial y las mejores prácticas. Si no estás seguro de una información, indícalo y sugiere consultar la documentación de AWS.”

4. Ajustar comportamiento del modelo

El

5. Subir recursos al entorno de chat
El modelo funcionará mejor si le proporcionas documentación oficial y materiales relevantes. Así podrá generar respuestas precisas y alineadas con los temas del examen. Esto garantiza que tu compañero de IA tenga acceso a los detalles específicos necesarios para una preparación eficaz del examen.

Junto con el documento adjunto:

“Please use this AWS Solutions Architect Associate exam guide as reference material for our discussion. This document contains the official exam content and objectives.”

Si procedes en español:

“Utiliza esta guía del examen AWS Solutions Architect Associate como material de referencia para nuestra conversación. Este documento contiene el contenido y los objetivos oficiales del examen.”

Cómo crear indicaciones de estudio efectivas

La ingeniería de prompts, es decir, la práctica de elaborar instrucciones eficaces para los modelos de IA, es todo un arte: la calidad de tu pregunta define la calidad de la respuesta. Cuanto más específica sea tu instrucción, más útil será el resultado.

Por ejemplo, en lugar de preguntar simplemente “¿Qué es Amazon S3?”, puedes formular “Menciona tres diferencias clave entre Amazon S3 y Amazon EBS para el examen de AWS Solutions Architect Associate”.

Algunas ideas de prompts prácticos:

Caso 1: Preguntas de práctica

“Generate a multiple choice, scenario-based question about S3 lifecycle policies. Include a detailed explanation for the correct answer.”

“Generate 5 practice multiple choice questions about Amazon RDS, and explain the correct answers thoroughly.”

“Generate a multiple choice question about AWS Lambda service pricing model.”

En español:

“Genera una pregunta de opción múltiple basada en escenarios sobre políticas de ciclo de vida en Amazon S3 y explica la respuesta correcta.”

“Crea cinco preguntas de práctica sobre Amazon Aurora y detalla las soluciones.”

“Genera una pregunta de opción sobre el modelo de pago del servicio AWS Lambda”.

Caso 2: Aprendizaje basado en escenarios

Tras realizar un laboratorio en AWS SimuLearn, puedes ampliar tu comprensión con prompts como:

“Explain how Auto Scaling works with ELB and CloudWatch using a real-world ecommerce scenario. Include key configuration considerations.”

Si continuas en español:

“Explica cómo funciona Auto Scaling con ELB y CloudWatch usando un escenario real de comercio electrónico. Incluye consideraciones clave en la configuración de todos los recursos.”

Caso 3: Refuerzo de conceptos

Para repasar fundamentos:

“Explain the AWS shared responsibility model using an analogy. Then highlight 3 key elements that frequently appear on the Solutions Architect Associate exam.”

Si la conversación es español:

“Explica el modelo de responsabilidad compartida de AWS usando una analogía y destaca tres puntos clave que suelen aparecer en el examen de Solutions Architect Associate.”

Equilibrar la IA con métodos tradicionales

La clave está en usar la IA como complemento, no como sustituto. Para ello, te aconsejo que crees bloques de estudio estructurados con pausas breves entre sesiones. La IA puede ayudarte a reforzar lo aprendido y resolver dudas, pero el conocimiento sólido viene del trabajo práctico y la constancia. Al mismo tiempo, puedes apoyarte en laboratorios de AWS, y de herramientas como AWS Cloud Quest.

Al final de cada sesión, puedes usar Bedrock para hacer comprobaciones rápidas de conceptos o practicar preguntas tipo examen. Y, por supuesto, verifica siempre tus respuestas con la documentación oficial.

Reflexión final

Mientras avanzas hacia tu certificación, prueba tu asistente de IA y descubre cómo puede simplificar temas complejos, ponerte a prueba con ejercicios personalizados y mantener tu motivación constante.

Si más adelante quieres ir un paso más allá, explora el Generative AI Application Builder en AWS para crear tus propias aplicaciones de IA.

Y recuerda, tu éxito depende tanto de la tecnología como de tu constancia. Combinar el poder de la IA con una estrategia de estudio disciplinada es, sin duda, la mejor manera de prepararte para cualquier certificación.

Adicionalmente, complementa tu estudio asistido por IA con los recursos de AWS Skill Builder y AWS Educate. Aprovecha y conecta con compañeros a través de la comunidad certificada de AWS para obtener apoyo adicional y oportunidades de networking.

---

Si este artículo te ha resultado útil o tienes alguna pregunta, puedes contactar conmigo en LinkedIn.
