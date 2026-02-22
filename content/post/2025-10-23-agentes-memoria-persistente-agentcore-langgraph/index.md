---
title: "Construyendo agentes con memoria persistente en AWS: Amazon Bedrock AgentCore + LangGraph"
slug: "agentes-memoria-persistente-agentcore-langgraph"
date: 2025-10-23T17:30:00+02:00
image: https://d1hvrz5kjt6fwi.cloudfront.net/Blog%20Posts%20Covers/Post%2005/Post%2005_horizontal_en.png
draft: false
description: De agentes “stateless” a sistemas inteligentes con memoria a largo plazo usando Amazon Bedrock AgentCore y LangGraph
tags:
  - AWS
  - AI
  - Strands Agents SDK
categories:
  - AWS
  - AI
  - AWS Developer Tools
---

# Construyendo agentes con memoria persistente usando LangGraph y Amazon Bedrock AgentCore

AWS lanzó **Amazon Bedrock AgentCore** para resolver uno de los grandes problemas de los agentes de IA en producción: **cómo desplegarlos y operarlos de forma segura, escalable y desacoplada del framework o modelo utilizado**.

En este post vamos un paso más allá.

Vamos a **combinar LangGraph con Amazon Bedrock AgentCore** para construir agentes de IA con **memoria persistente**, capaces de mantener el contexto de conversación **entre sesiones y reinicios del servidor**. Para ello, aprovecharemos las capacidades de gestión de memoria de AgentCore junto con la flexibilidad de orquestación que ofrece **LangGraph**, un framework open source basado en grafos.

Vamos al grano.

---

## Requisitos previos

Antes de empezar, asegúrate de tener:

* Una cuenta de AWS
* AWS CLI configurado localmente
* AWS CDK instalado
* Python 3
* Docker

---

## Qué vamos a cubrir en este post

A lo largo del artículo veremos:

* Qué es Amazon Bedrock AgentCore
* Qué es LangGraph y qué papel juega en la arquitectura
* Cuáles son los servicios principales de AgentCore
* Cómo implementar el agente paso a paso en Python
* Cómo probar memoria en local y en producción

---

## ¿Qué es Amazon Bedrock AgentCore?

Amazon Bedrock AgentCore es una **plataforma centrada en agentes** que permite **construir, desplegar y operar agentes de IA de forma segura y a escala**, sin tener que gestionar infraestructura.

AgentCore se estructura en tres grandes fases:

---

### Fase Build

Aquí es donde defines **qué puede hacer tu agente**.

* **AgentCore Gateway**
  Actúa como intermediario entre el agente y las herramientas. Permite convertir funciones Lambda o integraciones API en herramientas consumibles por el agente.

* **AgentCore Memory**
  Permite integrar memoria a corto y largo plazo que persiste entre sesiones. Es el componente clave de este post.

* **AgentCore Policy**
  Define límites y reglas de comportamiento alineadas con requisitos de negocio.

* **AgentCore Identity**
  Gestiona identidad, autenticación y acceso de los agentes de forma segura y escalable.

* **AgentCore Code Interpreter (tool integrada)**
  Proporciona un sandbox aislado para ejecución de código.

* **AgentCore Browser (tool integrada)**
  Ofrece un entorno de navegador cloud totalmente gestionado para interactuar con aplicaciones web.

---

### Fase Deploy

* **AgentCore Runtime**
  Runtime serverless diseñado específicamente para agentes: arranques rápidos, identidad integrada y escalado automático.

---

### Fase Operate

* **AgentCore Observability**
  Monitorización centralizada y debugging en producción.

* **AgentCore Evaluations**
  Métricas de calidad para evaluar y optimizar el comportamiento del agente.

---

## ¿Qué es LangGraph?

LangGraph es una librería que utiliza un **enfoque basado en grafos** para construir y gestionar flujos de agentes **stateful**.

En lugar de pensar en prompts aislados, defines nodos, estados y transiciones. Esto facilita:

* Mantener estado
* Controlar flujos complejos
* Integrar memoria
* Escalar lógica sin convertir el código en un caos

En este ejemplo construiremos un agente sencillo que recibe input del usuario y devuelve una respuesta, pero con una arquitectura preparada para crecer.

---

## Primeros pasos con AgentCore

Usaremos el **AgentCore Starter Toolkit**.

```bash
pip install bedrock-agentcore-starter-toolkit
agentcore create
```

Durante el asistente, seleccioné lo siguiente:

* Ruta del proyecto: `./pythonlanggraph`
* Tipo de inicio: agente listo para producción con CDK
* Framework: LangChain + LangGraph
* Proveedor de modelo: Amazon Bedrock
* Infraestructura como código: CDK
* Ejecutar `agentcore configure`: sí

> Nota: en el momento de escribir este post, el soporte de CDK para AgentCore sigue siendo experimental.

---

## Probando el agente localmente

Reemplaza `src/main.py` por el siguiente código base:

```python
import os
from langchain_core.messages import HumanMessage
from langchain.agents import create_agent
from bedrock_agentcore import BedrockAgentCoreApp
from model.load import load_model

app = BedrockAgentCoreApp()
llm = load_model()

@app.entrypoint
async def invoke(payload):
    graph = create_agent(llm)
    prompt = payload.get("prompt", "What is Agentic AI?")
    result = await graph.ainvoke({"messages": [HumanMessage(content=prompt)]})
    return {"result": result["messages"][-1].content}

if __name__ == "__main__":
    app.run()
```

Ejecuta el agente en local:

```bash
cd pythonlanggraph
agentcore dev
```

Y pruébalo desde otro terminal:

```bash
agentcore invoke --dev '{"prompt": "who are you?"}'
```

Todo funciona… pero **no hay memoria persistente**.

---

## Añadiendo memoria con LangGraph (solo local)

Ahora usamos `MemorySaver` de LangGraph para mantener contexto dentro de una sesión.

Esto funciona… **hasta que reinicias el servidor**.

En cuanto el proceso se reinicia, el estado se pierde.

Y aquí aparece el problema real.

---

## El problema: reinicios = amnesia

Aunque LangGraph permite memoria en ejecución, esta es:

* Volátil
* Dependiente del proceso
* No sobrevive a reinicios

Para resolver esto necesitamos **memoria externa y persistente**.

Aquí entra **Amazon Bedrock AgentCore Memory**.

---

## Integrando Amazon Bedrock AgentCore Memory

Ahora actualizamos el agente para:

* Guardar conversaciones en AgentCore Memory
* Restaurar contexto incluso tras reinicios
* Mantener sesiones coherentes usando `session_id`

El código crece, pero el concepto es claro:

* LangGraph → memoria inmediata
* AgentCore Memory → memoria persistente

El resultado es un agente que **recuerda incluso después de reiniciar el runtime**.

---

## Probando memoria persistente

1. Haces una pregunta
2. Reinicias el servidor
3. Vuelves a preguntar usando el mismo `session_id`
4. El agente recuerda

Eso confirma que la memoria ya no depende del proceso, sino de AgentCore.

> Importante: la extracción de memoria no es instantánea. Puede tardar unos segundos en estar disponible.

---

## Despliegue en producción con CDK

```bash
cd cdk
npm install
npm run cdk synth
npm run cdk deploy
```

Una vez desplegado, puedes invocar el agente pasando el `session_id`:

```bash
agentcore invoke '{"prompt": "Who are you?"}' --session-id 'user-123456-conversation-87654321'
```

Si usas otro `session_id`, se crea una nueva sesión.
Si reutilizas el anterior, el agente recuerda.

---

## Permisos necesarios

No olvides añadir permisos de AgentCore Memory al rol del runtime:

```ts
bedrock-agentcore:CreateMemory
bedrock-agentcore:CreateMemorySession
bedrock-agentcore:ListLongTermMemoryRecords
bedrock-agentcore:SearchLongTermMemories
```

Sin esto, la memoria no funcionará en producción.

---

## Observabilidad

LangGraph + LangSmith, combinado con:

* CloudWatch Logs
* AgentCore Observability

te da una **visión completa** tanto del comportamiento del agente como del uso de memoria.

Es especialmente útil para entender **qué se está almacenando** y **cuándo se recupera**.

---

## Limpieza de recursos

Cuando termines:

```bash
cdk destroy --all
```

Y elimina la memoria manualmente:

```bash
aws bedrock-agentcore-control delete-memory --memory-id <MEMORY_ID>
```

---

## Conclusión

La mayoría de agentes hoy en día **hablan bien**, pero **no recuerdan**. Y sin memoria, no hay continuidad, solo sesiones aisladas.

Al combinar **LangGraph** con **Amazon Bedrock AgentCore Memory**, pasamos de agentes stateless a **sistemas con contexto real**, preparados para producción.

No es solo una mejora técnica.
Es un cambio de paradigma.

A partir de aquí, los agentes dejan de ser demos… y empiezan a ser productos.

---

Si este artículo te ha resultado útil o tienes alguna pregunta, puedes contactar conmigo en [LinkedIn.](https://www.linkedin.com/in/briansuarezsantiago/)
