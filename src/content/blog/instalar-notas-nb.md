---
title: Instalar gestor de notas nb
author: Miguel Hernández Moreno
pubDatetime: 2024-08-31T12:28:00-06
slug: gestor-notas-nb
featured: false
draft: false
tags:
  - dev
  - tool
  - linux
  - notas
  - nb
description: Instalar gestor de notas CLI nb
---

[nb](https://github.com/xwmx/nb) toma de notas en texto plano, marcadores y archivado con CLI y web local, con vinculación, etiquetado, filtrado, búsqueda, versionado y sincronización con Git, conversión con Pandoc, y más, en un solo script portátil.

## Instalar desde github releases

```bash
curl -L https://raw.github.com/xwmx/nb/master/nb -o /usr/local/bin/nb &&\
sudo chmod +x /usr/local/bin/nb &&
sudo nb completions install
```
