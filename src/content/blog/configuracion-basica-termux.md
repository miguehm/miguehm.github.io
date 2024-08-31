---
title: Configuración basica de termux
author: Miguel Hernández Moreno
pubDatetime: 2024-08-31T12:55:00-06
slug: configuracion-basica-termux
featured: false
draft: false
tags:
  - dev
  - linux
  - termux
description: Imprescindible en la primera instalación de termux
---

## Contenidos

## Configurar clipboard

Instalar `termux-api` tanto en la terminal como la [apk](https://f-droid.org/es/packages/com.termux.api/) .

```bash 
pkg install termux-api
```

Modo de uso

```bash
echo "hola" | termux-clipdoard-set
```

## Montar sdcard0

```bash
termux-setup-storage
```
