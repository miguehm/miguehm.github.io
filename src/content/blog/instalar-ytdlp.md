---
title: Descargar video/audio de youtube desde terminal
author: Miguel Hernández Moreno
pubDatetime: 2024-08-31T13:08:00-06
slug: descargar-video-audio-youtube-terminal
featured: false
draft: false
tags:
  - dev
  - tool
  - linux
  - youtube
  - ytdlp
description: Descargar video o audio de cualquier video de youtube desde la terminal Linux
---

[Repositorio oficial](https://github.com/yt-dlp/yt-dlp) 

## Contenidos

## Instalación

Con python

```bash 
pip install yt-dlp
```

Binario

```bash 
curl -O https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp
```

## Descargar solo un fragmento

```bash
yt-dlp -f 22 --download-sections "*0:01:00-0:01:30" https://youtu.be/lrDaEtYA25Y
```

> Tiempo en segundos

## Listar formatos disponibles

```bash
yt-dlp --list-formats https://youtu.be/lrDaEtYA25Y
```

## Extraer audio

```bash
yt-dlp -x --audio-format mp3 --audio-quality 8 https://www.youtube.com/watch\?v\=Bv-1BnoB75k
```

> audio-quality defecto: 5, minimo: 1, maximo 10

## Enlaces Útiles

- [Reddit parts video](https://www.reddit.com/r/youtubedl/wiki/howdoidownloadpartsofavideo/) 
