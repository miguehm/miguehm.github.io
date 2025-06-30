---
title: AWS Lambda + API REST Cheatsheet
author: Miguel Hernández
pubDatetime: 2025-06-30T11:25:00-06
slug: lambda-api-rest-aws
featured: true
draft: false
tags:
  - dev
  - aws
  - python
  - API
ogImage: ""
description: Apuntes de AWS Lambda y ejemplo basico de API REST
canonicalURL: AWS Lambda, usa básico.
---

# Crear una función Lambda (desde consola)

1. Ir a [AWS Lambda](https://console.aws.amazon.com/lambda/)
2. Click en **"Create function"**
3. Elegir:
   - **Author from scratch**
   - Nombre: `miLambdaAPI`
   - Runtime: `Python 3.12` (o el que prefieras)
   - Rol: crear uno nuevo con permisos básicos (`AWSLambdaBasicExecutionRole`)

## Codigo de ejemplo

```python
import json

def lambda_handler(event, context):
    try:
        body = json.loads(event.get('body', '{}'))
    except json.JSONDecodeError:
        return {
            'statusCode': 400,
            'body': 'Invalid JSON'
        }

    if 'age' not in body:
        return {
            'statusCode': 400,
            'body': 'Missing age'
        }

    if body['age'] < 18:
        return {
            'statusCode': 400,
            'body': 'Age must be greater than 18'
        }

    return {
        'statusCode': 200,
        'body': json.dumps({'message': 'OK', 'age': body['age']})
    }
```

---

# Crear una API REST con API Gateway (Lambda Proxy)

1. Ir a **API Gateway** → “Create API”
2. Elegir **REST API** (no "HTTP API")
3. Nombre: `miAPI`
4. Activar **Lambda Proxy Integration**

## Crear un recurso y método

1. En el panel izquierdo:
   - Click en **Actions → Create Resource**
   - Nombre: `/hello`
2. Seleccionar el recurso `/hello` → **Actions → Create Method → GET o POST**
3. Seleccionar **Lambda Function**
   - Marcar “Use Lambda Proxy integration” ✅
   - Ingresar el nombre de tu Lambda (`miLambdaAPI`)
4. Dar permisos cuando lo solicite

## Deploy de la API

1. Click en **Actions → Deploy API**
2. Crear un nuevo **Stage** (ej: `test`)
3. Se obtendrá una URL como:

```
https://abc123.execute-api.us-east-1.amazonaws.com/test/hola
```

---

# Probar con `curl`

```bash
curl -X POST https://abc123.execute-api.us-east-1.amazonaws.com/dev/hola \
  -H "Content-Type: application/json" \
  -d '{"age": 21}'
```

## Respuesta esperada:

```json
{
  "message": "OK",
  "age": 21
}
```

---

# Dudas

## ¿Por qué aparece `body` en el `event` de Lambda?

**API Gateway lo añade automáticamente** cuando usas **Lambda Proxy Integration**. Transforma la petición HTTP en un objeto `event` con esta estructura:

```json
{
  "resource": "/hola",
  "path": "/hola",
  "httpMethod": "POST",
  "headers": {
    "Content-Type": "application/json"
  },
  "body": "{\"age\": 21}",
  "isBase64Encoded": false
}
```

Entonces:

- El `body` es una cadena JSON, no un dict.
- Necesitas usar `json.loads(event['body'])` para acceder a los datos reales.

---

# Fuentes

- [Documentación oficial de Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) o pedir más ejemplos aquí.
