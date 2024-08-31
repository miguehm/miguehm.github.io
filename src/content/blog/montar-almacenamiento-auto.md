---
title: Montar almacenamiento externo automaticamente
author: Miguel Hernández Moreno
pubDatetime: 2024-08-31T12:28:00-06
slug: configurar-almacenamiento-auto-linux
featured: false
draft: false
tags:
  - dev
  - linux
  - almacenamiento
  - udiskie
description: Montar unidades de almacenamiento inmediatamente al conectarse en el equipo usando udiskie
---

## Instalar udiskie

```bash
pacman -S udiskie udisks2
```

## Agregar udiskie al archivo bspwmrc

```bash
# configuracion previa
udiskie &
```
