---
title: Configurar brillo de pantalla en Linux
author: Miguel Hernández Moreno
pubDatetime: 2024-08-31T11:58:00-06
slug: configurar-brillo-pantalla-linux
featured: false
draft: false
tags:
  - dev
  - linux
  - x11
  - xorg
  - pantalla
  - sxhkd
description: Configurar atajo de teclado para modificar el brillo de la pantalla en Linux para gestores de ventanas basados en x11
---

## Instalar brightnessctl

```bash 
sudo pacman -S brightnessctl
```

Agregar al archivo `sxhkdrc`

```bash 
# Aumentar brillo
alt + {F2, Up}
   brightnessctl s 10%+

# Disminuir brillo
alt + {F1, Down}
   brightnessctl s 10%-
```

> Se opta por usar la tecla Alt por su facilidad de configuración, la tecla Fn no es directamente manejable por sxhkd o la mayoría de los gestores de atajos de teclado, ya que su comportamiento es generalmente gestionado a nivel de hardware y firmware del teclado.
