---
title: Agregar nuevas credenciales a Github
author: Miguel Hernández Moreno
pubDatetime: 2024-08-31T12:55:00-06
slug: agregar-credenciales-github
featured: false
draft: false
tags:
  - dev
  - linux
  - github
description: Agregar credenciales a github para gestionar repositorios remotos
---

## Instalar Open SSH

```bash
pkg install openssh
```

## Generar clave publica

```bash
ssh-keygen -t ed25519 -C "miguel.hernandezmor@alumno.buap.mx"

```

## Añadir clave publica a la cuenta de github

```bash
cat ~/.ssh/id_ed25519.pub
```

Agregar a las configuraciones de la cuenta github en `configuraciones > claves ssh > añadir nueva`.
