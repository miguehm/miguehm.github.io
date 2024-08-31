---
title: Configurar mousepad en Linux 
author: Miguel Hernández Moreno
pubDatetime: 2024-08-31T10:48:00-06
slug: configurar-mousepad-linux
featured: false
draft: false
tags:
  - dev
  - linux
  - x11
  - xorg
  - mousepad
description: Configurar el comportamiento del mousepad para gestores de ventanas basados en x11
---

Xorg es el servidor de display para el sistema de ventanas X, que proporciona la infraestructura básica para la interfaz gráfica de usuario en sistemas Unix y Unix-like, como Linux. Xorg maneja la comunicación entre el hardware gráfico del sistema y el software, permitiendo que las aplicaciones dibujen en la pantalla y reciban entradas del teclado y el ratón.

## Configurando el mousepad

Crear un archivo en `/etc/X11/xorg.conf.d/30-touchpad.conf`

```bash
Section "InputClass"
    Identifier "touchpad"
    Driver "libinput"
    MatchIsTouchpad "on"
    Option "Tapping" "on"
    Option "TappingButtonMap" "lmr"
EndSection
```
