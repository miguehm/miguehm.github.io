---
title: Plan 6 Semanas. Django + APIs desde Cero
author: Miguel Hernández
pubDatetime: 2025-04-16T21:24:00Z
slug: django-api-desde-cero
featured: true
draft: false
tags:
  - dev
  - python
  - django
  - redes
  - practicas
ogImage: ""
description: Apuntes de practicas profesionales.
canonicalURL: Bases para backend.
---

# Semana 1: Fundamentos Web y APIs

## Que es internet, como funciona la web

El internet es una red global de computadoras conectadas entre ellas, se comunican mediante un conjunto estandarizado de protocolos.

Nació como una estrategia para mantener las comunicaciones en caso de una guerra nuclear. ([youtube](https://youtu.be/Dxcc6ycZ73M&t=1m03s))

---

## Que es HTTP y como se comunican cliente-servidor

> ISP: Internet Service Provider

Hyper Text Transfer Protocol **HTTP**, es el lenguaje en el que se comunica un cliente y un servidor.

El internet es _una filosofia de diseño_ y una arquitectura expresada en un conjunto de protocolos, necesarios para una comunicación cliente-servidor.

Un protocolo es un conjunto de reglas estandarizadas para la comunicación entre máquinas.

Cada dispositivo necesita una dirección única, uno de los protocolos mas importantes es Internet Protocol **IP**. La dirección unica de los dispositivos conectados a internet se llama dirección IP.

El _domain name system_ **DNS** asocia direcciones url (uniform resource locator) con su respectiva dirección IP.

La información suele ser transmitida por paquetes, un paquete es una fracción de esa información, esto es para mejorar la eficiencia de envio de información en la red.

El Transmission Control Protocol **TCP** administra el envio y la recepción de los paquetes. Cuando la información es fraccionada en paquetes, TCP se encarga de verificar que la información esté completa, en caso afirmativo es marcada como completa, sin embargo, si parte de los paquetes no han llegado al destino, TCP lo notifica para que sean reenviados.

---

## Metodos http: GET, POST, PUT, DELETE, PATCH

- GET: **Solicita** información de un recurso.
- POST: **Envia** información al servidor.
- PUT: **Actualiza** o crea informacion existente.
- DELETE: **Elimina** un recurso identificado en la URI (Uniform Resource Identifier, identifica un recurso por su nombre, por su ubicación o por ambos).
- PATCH: **Modifica** parcialmente un recurso, a diferencia de PUT que modifica totalmente la información.

---

## Que es una API

Application Programming Interface, son un conjunto de reglas que permiten que aplicaciones se comuniquen entre sí (intercambio de datos, funcionalidades y servicios).

---

## Que es una API Restful

Representational State Transfer, es un estilo de diseño que define como los sistemas se comunican en la red. Emplea formatos como JSON o XML para el intercambio de datos. Los clientes pueden llamar a la API sin importar su implementación interna.

---

## Headers, status codes, json

### Headers

Permiten al cliente y al servidor enviar información adicional junto a una peticion o respuesta.

### Status Codes

Son códigos emitidos por un servidor en respuesta a la solicitud de un cliente.

- 1xx: Informativos.
- 2xx: Éxito.
- 3xx: Redirecciones.
- 4xx: Errores del cliente.
- 5xx: Errores del servidor.

## Comandos útiles cUrl

```bash
# GET
curl https://www.example.com # HTML content
curl -X GET https://www.example.com # Explicit

# Save response
curl -o file.html https://www.example.com

# Send data via POST
curl -X POST -d '{"title": "Nueva tarea", "body": "Mi tarea de prueba", "userId": 1}' -H "Content-Type: application/json; charset=UTF-8" https://jsonplaceholder.typicode.com/posts/
# -X POST: Petition type
# -d: data to send

# Send a file as POST (like a JSON file)
curl -X POST -H "Content-Type: application/json" -d @data.json https://www.example.com
# -H: add HTTP header
# -d @data.json: read data.json content and send it

# Update a Resource via PUT
curl -X PUT -d '{"id": 101, "title": "Tarea actualizada", "body": "He actualizado con PUT", "userId": 1}' -H "Content-Type: application/json; charset=UTF-8" https://jsonplaceholder.typicode.com/posts/1

# Update partial via PATCH
curl -X PATCH -d '{"title": "Tarea modificada con PATCH"}' -H "Content-Type: application/json; charset=UTF-8" https://jsonplaceholder.typicode.com/posts/1

# Save a file
curl -O https://www.example.com/image.jpg

```

---

## Fuentes

- [roadmap.sh](https://roadmap.sh/guides/what-is-internet)
