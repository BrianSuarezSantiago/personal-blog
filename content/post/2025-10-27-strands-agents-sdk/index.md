---
title: "Strands Agents 101: Introducción práctica a los agentes de IA en AWS"
slug: "strands-agents-101-introduccion-practica"
date: 2025-10-20T17:30:00+02:00
image: https://d1hvrz5kjt6fwi.cloudfront.net/Blog%20Posts%20Covers/Post%2005/Post%2005_horizontal_en.png
draft: false
description: Una guía paso a paso para principiantes usando Strands Agents y Amazon Bedrock
tags:
  - AWS
  - Strands Agents SDK
  - AI
categories:
  - AWS
  - AI
  - AWS Developer Tools
---

El desarrollo de la IA está cambiando rápidamente, pasando de interacciones simples basadas en prompts a sistemas agénticos: IA que puede razonar, planificar y ejecutar acciones. El SDK de Strands Agents es un framework open source de AWS diseñado para simplificar la construcción de este tipo de agentes siguiendo un enfoque impulsado por el modelo, en el que los modelos de lenguaje grandes (LLM) se encargan del razonamiento y de la selección de herramientas con un código de orquestación mínimo.

En lugar de dedicar meses a construir pipelines complejos, Strands permite a los desarrolladores crear agentes inteligentes y listos para producción en cuestión de días. Esta guía introduce a los principiantes en los fundamentos, los recursos necesarios y los bloques de construcción principales para empezar con confianza.

# ¿Qué es Strands Agents?

Strands Agents es un SDK en Python que permite a los desarrolladores crear agentes de IA capaces de:

- Razonar y planificar utilizando LLMs.
- Llamar a herramientas (funciones) para realizar acciones reales.
- Integrarse de forma natural con servicios de AWS.
- Ejecutarse sobre múltiples proveedores de LLM.

A diferencia de los scripts tradicionales de chatbots, Strands se centra en el diseño de agentes preparados para producción, haciendo énfasis en la observabilidad, la flexibilidad y la reducción de boilerplate.

# Conceptos clave de Strands Agents

La definición más simple de un agente es la combinación de tres elementos:

1. **Modelo LLM**
2. **Herramientas**
3. **Un prompt**

![Propmpt Diagram](./prompt-diagram.png)

El agente utiliza estos tres componentes para completar una tarea, a menudo de forma autónoma. La tarea del agente puede ser responder una pregunta, generar código, planificar unas vacaciones u optimizar una cartera financiera. En un enfoque impulsado por el modelo, el agente utiliza el modelo para dirigir dinámicamente sus propios pasos y para usar herramientas con el fin de completar la tarea especificada.

Para definir un agente con el SDK de Strands Agents, se definen estos tres componentes en el código:

## Modelo

Strands ofrece un soporte flexible de modelos. Puedes utilizar:

- Cualquier modelo de Amazon Bedrock que soporte uso de herramientas y streaming
- Un modelo de la familia Claude de Anthropic a través de la API de Anthropic
- Un modelo de la familia Llama mediante la API de Llama
- Ollama para desarrollo local
- Muchos otros proveedores como OpenAI a través de LiteLLM
- Tu propio proveedor de modelos personalizado con Strands

## Herramientas

Puedes elegir entre miles de servidores publicados del Model Context Protocol (MCP) para usarlos como herramientas de tu agente. Strands también proporciona más de 20 herramientas de ejemplo preconstruidas, incluidas herramientas para manipular archivos, realizar peticiones API e interactuar con APIs de AWS. Cualquier función en Python puede usarse fácilmente como herramienta simplemente utilizando el decorador `@tool` de Strands.

## Prompt

Proporcionas un prompt en lenguaje natural que define la tarea del agente, como responder una pregunta de un usuario final. También puedes proporcionar un system prompt que define instrucciones generales y el comportamiento deseado del agente.

Un agente interactúa con su modelo y sus herramientas en un bucle hasta completar la tarea proporcionada por el prompt. Este bucle agéntico es el núcleo de las capacidades de Strands. El bucle agéntico de Strands aprovecha al máximo lo potentes que se han vuelto los LLM y su capacidad nativa para razonar, planificar y seleccionar herramientas.

En cada iteración, Strands invoca al LLM con el prompt y el contexto del agente, junto con una descripción de las herramientas disponibles. El LLM puede:

- Responder en lenguaje natural al usuario final
- Planificar una serie de pasos
- Reflexionar sobre pasos anteriores del agente
- Seleccionar una o más herramientas para usar

Cuando el LLM selecciona una herramienta, Strands se encarga de ejecutarla y de devolver el resultado al LLM. Cuando el LLM completa su tarea, Strands devuelve el resultado final del agente.

### Filosofía de diseño principal (enfoque impulsado por el modelo)

Strands desplaza la complejidad desde el desarrollador hacia el propio modelo. En lugar de codificar árboles de decisión o flujos de trabajo:

- El LLM razona sobre la tarea.
- El LLM decide cuándo usar herramientas.
- El desarrollador define capacidades, no el flujo de control.

Esto reduce drásticamente el código de orquestación y mejora la mantenibilidad.

# Cómo funcionan los Strands Agents (Agentic Loop)

Para entender realmente por qué Strands requiere tan poco código de orquestación, es útil observar cómo opera internamente un agente. En el núcleo de Strands se encuentra un **bucle agéntico**, en el que el agente razona, actúa y refina continuamente su respuesta coordinando un modelo y un conjunto de herramientas.

![Bucle agéntico de Strands Agents](./agentic-loop.png)

Este bucle comienza cuando un prompt del usuario invoca al agente. El agente reenvía el prompt al modelo, que razona sobre la solicitud y decide si puede responder directamente o si necesita usar una herramienta. Si se requiere una herramienta, el agente la ejecuta y pasa el resultado de vuelta al modelo para un razonamiento adicional. Este ciclo puede repetirse varias veces hasta que el modelo determina que la tarea está completa.

Una vez finalizado el razonamiento, el agente devuelve una respuesta final, bien estructurada, al usuario. Dado que el propio modelo decide cuándo y cómo usar las herramientas, los desarrolladores no necesitan escribir flujos de trabajo complejos, comprobaciones condicionales ni máquinas de estados. Esto es lo que hace que Strands sea a la vez potente y accesible para principiantes.

En resumen, los agentes de Strands no solo responden: piensan, actúan y se adaptan a través de este agentic loop.

## Proveedor de modelos por defecto: Amazon Bedrock

Por defecto, Strands utiliza Amazon Bedrock, un servicio totalmente gestionado de AWS que proporciona acceso a modelos fundacionales de alta calidad, como Anthropic Claude, a través de una API unificada.

AWS Bedrock se encarga de:

- Infraestructura y escalado
- Selección y actualización de modelos
- Seguridad e integración con IAM

🔗 [AWS Bedrock Overview](https://aws.amazon.com/bedrock/)

## Herramientas de Strands Agents

Strands proporciona un ecosistema rico de herramientas a través del repositorio oficial de herramientas. Estas herramientas permiten que los agentes vayan más allá de la generación de texto y realicen operaciones reales.

🔗 [Strands Agents Tools (Repositorio oficial)](https://github.com/strands-agents/tools)

Las capacidades disponibles incluyen:

- Cálculo matemático y simbólico
- Lectura y escritura de archivos
- Peticiones HTTP
- Ejecución de Python
- Interacción con servicios de AWS
- Memoria y recuperación de conocimiento

## Requisitos previos

Antes de empezar, asegúrate de contar con:

- Python 3.10 o superior
- Una cuenta de AWS configurada localmente
- Conocimientos básicos de Python (recomendado)

## Instalación

Instala el SDK de Strands y el paquete de herramientas usando pip:

```bash
pip install strands-agents strands-agents-tools
```

Una vez instalado, ya puedes crear tu primer agente.

# Creando tu primer agente con Strands

Un agente de Strands puede inicializarse únicamente con un system prompt:

```python
from strands import Agent

agent = Agent(
    system_prompt="You are a helpful assistant that provides concise answers."
)

response = agent("Hello! What can you do?")
print(response)
```

Por defecto, este agente se ejecuta sobre AWS Bedrock utilizando un modelo Claude seleccionado según tu región de AWS, sin necesidad de configuración manual del modelo.

# Añadiendo herramientas a un agente

Las herramientas otorgan a los agentes la capacidad de actuar. Strands soporta herramientas integradas y herramientas personalizadas mediante el decorador `@tool`.

```python
from strands import Agent, tool
from strands_tools import calculator

@tool
def weather():
    """Return current weather (dummy implementation)."""
    return "sunny"

agent = Agent(
    tools=[calculator, weather],
    system_prompt="You can perform calculations and provide weather updates."
)

print(agent("What is 12 * 8?"))
```

## Invocación directa de herramientas (depuración y pruebas)

Las herramientas también pueden invocarse directamente sin pasar por el bucle de razonamiento del agente:

```python
agent.tool.calculator(
    expression="x^2 + 2x",
    mode="derive",
    wrt="x"
)
```

Esto resulta útil para depurar, probar lógica o utilizar herramientas dentro de otras herramientas.

# Logging y observabilidad

Strands se integra con el módulo estándar de logging de Python, proporcionando visibilidad sobre el comportamiento del agente:

```python
import logging
from strands import Agent

logging.getLogger("strands").setLevel(logging.DEBUG)

logging.basicConfig(
    format="%(levelname)s | %(name)s | %(message)s",
    handlers=[logging.StreamHandler()]
)

agent = Agent()
agent("Hello!")
```

Esto ayuda a rastrear:

- Decisiones del modelo
- Uso de herramientas
- Errores y comportamiento de recuperación

# Proveedores de modelos en Strands

Aunque AWS Bedrock es el proveedor por defecto, Strands soporta múltiples proveedores de LLM:

- Amazon Bedrock
- APIs compatibles con OpenAI
- Anthropic
- LiteLLM
- Ollama (modelos locales)
- Proveedores personalizados

## Especificar explícitamente un modelo de Bedrock

```python
from strands import Agent

agent = Agent(
    model="us.anthropic.claude-3-7-sonnet-20250219-v1:0"
)

print(agent.model.config)
```

### Configuración avanzada de modelos

Para un control más preciso, puedes configurar directamente un proveedor:

```python
from strands.models import BedrockModel
from strands import Agent

bedrock_model = BedrockModel(
    model_id="us.anthropic.claude-3-7-sonnet-20250219-v1:0",
    region_name="us-west-2",
    temperature=0.3
)

agent = Agent(model=bedrock_model)
```

# Por qué Strands es accesible para principiantes

Strands hace que el desarrollo de agentes sea más sencillo al:

- Reducir el boilerplate de orquestación
- Utilizar abstracciones claras y declarativas
- Soportar logging estándar de la industria
- Ofrecer integraciones flexibles de modelos
- Alinearse de forma natural con los flujos de trabajo de AWS

# Recursos necesarios para principiantes

- **Strands SDK**: [https://github.com/strands-agents/sdk-python](https://github.com/strands-agents/sdk-python)
- **Strands Tools**: [https://github.com/strands-agents/tools](https://github.com/strands-agents/tools)
- **AWS Bedrock**: [https://aws.amazon.com/bedrock/](https://aws.amazon.com/bedrock/)
- **Documentación oficial**: [https://strandsagents.com/latest](https://strandsagents.com/latest)

# Conclusión

Strands Agents proporciona una base limpia y preparada para el futuro para cualquiera que empiece con IA agéntica. Al permitir que los modelos gestionen el razonamiento y la toma de decisiones, Strands permite a los desarrolladores centrarse en definir capacidades en lugar de gestionar la complejidad.

Esta guía para principiantes cubre todo lo necesario para empezar. A medida que tus casos de uso crecen, Strands escala de forma fluida: desde agentes simples hasta sistemas autónomos y multiagente.

---

Si este artículo te ha resultado útil o tienes alguna pregunta, puedes contactar conmigo en [LinkedIn.](https://www.linkedin.com/in/briansuarezsantiago/)
