---
title: Jinja Cheatsheet
author: Miguel Hernández
pubDatetime: 2025-04-14T19:49:07Z
slug: jinja-cheatsheet
featured: true
draft: false
tags:
  - dev
  - python
  - django
ogImage: ""
description: Apuntes de Jinja
canonicalURL: Templater de Django.
---

## Sintaxis Básica

- **Variables**: `{{ variable }}`
- **Expresiones**: `{{ 2 + 2 }}` → `4`
- **Comentarios**: `{# Esto es un comentario #}`
- **Declaraciones**: `{% instrucción %}`

## Estructuras de Control

### Condicionales

```jinja
{% if user.is_authenticated %}
  Bienvenido, {{ user.username }}.
{% else %}
  Por favor, inicia sesión.
{% endif %}
```

### Bucles

```jinja
{% for item in lista %}
  {{ loop.index }}: {{ item }}
{% endfor %}
```

- `loop.index`: Índice (comenzando en 1)
- `loop.first`: Verdadero si es la primera iteración
- `loop.last`: Verdadero si es la última iteración

## Pruebas

```jinja
{% if variable is defined %}
  La variable está definida.
{% endif %}

{% if variable is none %}
  La variable es None.
{% endif %}

{% if numero is even %}
  El número es par.
{% endif %}
```

## Filtros Comunes

```jinja
{{ texto | lower }}       → "texto en minúsculas"
{{ texto | upper }}       → "TEXTO EN MAYÚSCULAS"
{{ texto | capitalize }}  → "Texto capitalizado"
{{ lista | length }}      → Longitud de la lista
{{ texto | replace("a", "o") }} → Reemplaza "a" por "o"
```

## Macros

```jinja
{% macro saludo(nombre) %}
  ¡Hola, {{ nombre }}!
{% endmacro %}

{{ saludo("Carlos") }}
```
