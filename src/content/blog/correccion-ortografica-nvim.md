---
title: Agregar lenguaje Español a neovim
author: Miguel Hernández Moreno
pubDatetime: 2024-08-31T12:28:00-06
slug: correccion-espanol-neovim
featured: false
draft: false
tags:
  - dev
  - linux
  - notas
  - nb
description: Instalar lenguaje Español para corrección ortografica en neovim
---

Ejecutar neovim sin cargar ningún archivo de configuración.

```bash
nvim -u NORC
```

Dentro de nvim

```vim
:set spelllang=es
:set spell
```
