---
title: Sincronizar notas de Obsidian en Android
author: Miguel Hernández Moreno
pubDatetime: 2024-08-31T12:28:00-06
featured: false
draft: false
tags:
  - dev
  - linux
  - obsidian
  - notas
  - termux
  - crond
description: Sincronizar notas con Termux y crond
---

## Contenidos

## Requisitos

- Termux instalado (version de github)
- Crear [shell script de sincronización](https://miguehm.github.io/posts/ejecutar-bashscript-frecuentemente/)

## Instalar termux services

Contiene scripts que controlan servicios.

```shell
pkg install termux-services
```

## Instalar cronie

Daemon que corre programas especificos cada cierto tiempo

```shell
pkg install cronie
```

## Establecer el "job" para ejecutar el shell script

```bash
crontab -e
```

Se abrirá un editor de texto, ingresar lo siguiente:

```
* * * * * bash $HOME/.scripts/sync.sh
```

Ejecutara el script cada minuto.

## Gestión del servicio

Para activarlo

```shell
sv-enable crond
```

Para detenerlo

```shell
sv down crond
```

Revisar el estado del servicio

```shell
sv status crond
```
