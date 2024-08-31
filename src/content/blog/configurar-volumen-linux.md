---
title: Configurar teclas de volumen en Linux
author: Miguel Hernández Moreno
pubDatetime: 2024-08-31T12:26:00-06
slug: configurar-volumen-linux
featured: false
draft: false
tags:
  - dev
  - linux
  - x11
  - xorg
  - volumen
  - sxhkd
description: Configurar atajo de teclado para modificar el volumen en Linux para gestores de ventanas basados en x11
---

## Instalar pulseaudio

```bash 
sudo pacman -S pulseaudio
```

## Agregar al archivo sxhkdrc

```bash 
# Subir y bajar volumen
alt + {F9, F10}
	pactl set-sink-volume @DEFAULT_SINK@ {-5%,+5%}

# Silenciar/activar sonido
alt + F8
	pactl set-sink-mute @DEFAULT_SINK@ toggle
```
