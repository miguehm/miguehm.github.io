---
title: Configurar distribución de teclado en linux
author: Miguel Hernández Moreno
pubDatetime: 2024-08-31T11:10:00-06
slug: configurar-teclado-dvorak-linux
featured: false
draft: false
tags:
  - dev
  - linux
  - x11
  - xorg
  - teclado
  - sxhkd
description: Configurar la distribución del teclado a dvorak en Linux en gestores de ventanas basados en x11
---

## Primer método: Configurar distribución de forma permanente

Instalar paquete de configuraciones de teclado.

```bash
sudo pacman -S xkeyboard-config
```

`xkeyboard-config` contiene configuraciones de teclado para el servidor X (Xorg), incluyendo distribuciones de teclado y opciones de configuración que permiten personalizar el comportamiento del teclado en el entorno gráfico.

Crear un archivo en `sudo nano /etc/X11/xorg.conf.d/00-keyboard.conf` con la siguiente configuración:

```bash
Section "InputClass"
    Identifier "keyboard"
    MatchIsKeyboard "on"
    Option "XkbLayout" "us"
    Option "XkbVariant" "dvorak-intl"
    Option "XkbOptions" "lv3:ralt_switch,compose:ralt"
EndSection
```

## Segundo método: Cambiar distribución de forma temporal

Instalar `xorg-xkb-utils`

```bash 
sudo pacman -S xorg-xkb-utils
```

Para la distribución **dvorak** con layout estadounidense, variante Dvorak Internacional, y estableciendo la tecla Alt derecha (RAlt) tanto para acceder al tercer nivel de caracteres como para la composición de caracteres

```bash 
setxkbmap -layout us -variant dvorak-intl -option lv3:ralt_switch -option compose:ralt
```

Configurar teclado **qwerty** en Español.

```bash 
setxkbmap -layout es
```

## Asignar un atajo para intercambiar teclados con sxhkd

Agregar al archivo `sxhkdrc`

```bash 
# Cambiar a layout español
alt + 1
   setxkbmap -layout us -variant dvorak-intl -option lv3:ralt_switch -option compose:ralt

# Cambiar a layout Dvorak Internacional
alt + 2
   setxkbmap -layout es
```
