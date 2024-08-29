---
title: Conectarse a una pantalla por hdmi con xrandr 
author: Miguel Hernández Moreno
pubDatetime: 2024-08-28T16:44:00-06
slug: snippets-custom-astrovim
featured: true
draft: false
tags:
  - dev
  - tool
  - hdmi
  - xrandr
description: Usar xrandr para conectarse a una pantalla externa.
---

Xrandr es una utilidad de línea de comandos para el sistema de ventanas X (X Window System) que permite configurar y gestionar las propiedades de las pantallas y monitores. Con xrandr, puedes cambiar la resolución, rotar la pantalla, ajustar la tasa de refresco, y gestionar múltiples monitores, entre otras funciones.

## Buscar pantalla con xrandr

```bash
xrandr
```

## Ajustar el HDMI destino al modo mas cercano a la fuente de imagen

```bash
xrandr --output HDMI-2 --mode 1360x768 --same-as eDP-1
```

## Reiniciar el display-manager

```bash
sudo systemctl restart display-manager
```

> Ajustar nuevamente el HDMI destino si los cambios aún no surten efecto.

## Guardar la configuración

```bash
autorandr --save hdmi-new-screen
```
