---
title: Ejecutar un bash script de fondo cada X segundos 
author: Miguel Hernández Moreno
pubDatetime: 2024-08-28T23:34:50-06
modDatetime: 2024-08-29T11:03:03-06
slug: ejecutar-bashscript-frecuentemente
featured: true
draft: false
tags:
  - dev
  - tool
  - linux
  - git
  - systemd
  - obsidian
  - nb
  - notas
description: Ejecutar un script de terminal cada cierto tiempo con la intención de sincronizar mis notas en todos mis dispositivos.
---

## Contenidos

## Crear el bash script y configurarlo

Este ejemplo es para sincronizar mi carpeta `notes` alojada en un repositorio remoto de Github.

Script `sync.sh`

```bash
#!/bin/bash                                                                   
                                                                               
# Configura tu repositorio y rama                                             
REPO_DIR="/home/miguehm/.nb/notes"                                             
BRANCH="main"                                                                 
                                                                              
# Cambia al directorio del repositorio                                        
cd $REPO_DIR                                                                  
                                                                              
# Actualiza el repositorio local                                              
git pull origin $BRANCH                                                       
                                                                              
# Añade y comitea los cambios locales                                         
git add .                                                                     
git commit -m "Sincronización automática"                                     
                                                                              
# Intenta hacer push                                                          
git push origin $BRANCH                                                       
                                                                              
# Si el push falla, intenta hacer rebase                                      
if [ $? -ne 0 ]; then                                                         
    git pull --rebase origin $BRANCH                                          
    git push origin $BRANCH                                                   
fi
```

Darle permisos de ejecución

```bash 
chmod +x sync.sh
```

## Archivos service, timer y el sistema systemd

`systemd` es un sistema de inicialización y gestor de servicios para sistemas operativos basados en Linux. Utiliza archivos de configuración llamados *units* (`.service`, `.timer`, `.sockets`, etc.) para definir y gestionar servicios, temporizadores, sockets, dispositivos, y más.

Un `service` puede ser cualquier tipo de tarea que se ejecute en segundo plano, como un servidor web, un script o una aplicación.


Un `timer` configuran temporizadores que activan servicios en momentos específicos o a intervalos regulares. Son útiles para tareas programadas, como copias de seguridad, sincronización de archivos o cualquier tarea que necesite ejecutarse periódicamente.

> `.service` define **que** se debe hacer, `.timer` define **cuando** se debe hacer.

Para nuestro caso particular, los archivos se guardaran en las siguiente ruta:

```bash
mkdir -p ~/.config/systemd/user/
```

## Creando archivo service

```service 
[Unit]
Description=Run sync.sh for my notes

[Service]
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
ExecStart=/home/miguehm/.scripts/sync.sh

[Install]
WantedBy=default.target
```

- `[Unit]`: Información sobre el servicio, como su descripción y             
  dependencias.                                                            
- `[Service]`: Configuración específica del servicio, como establecer variables de entorno (Enviroment), el comando a ejecutar (ExecStart), el usuario bajo el cual se ejecuta, y otras opciones de ejecución.                                                   
- `[Install]`: Define como y cuando la unidad debe ser habilitada. WantedBy=default.target en el archivo de servicio indica este servicio debe iniciar automáticamente como parte del proceso de arranque normal del sistema.

> Se establece la directia Enviroment en [Service] para que el servicio encuentre `git`.

## Creando archivo timer

```timer 
[Unit]
Description=Run sync.sh for my notes every 30 seconds

[Timer]
OnBootSec=30s
OnUnitActiveSec=30s
Unit=sync-notes.service

[Install]
WantedBy=timers.target
```

- `[Unit]`: Información sobre el temporizador, como su descripción.          
- `[Timer]`: Configuración del temporizador, como cuándo debe activarse (OnBootSec, OnUnitActiveSec), y a qué servicio está asociado (Unit).
- `[Install]`: Información sobre cómo y cuándo debe iniciarse el temporizador, como las unidades de destino (WantedBy).

## Inicialización y puesta en marcha del servicio

`systemctl` es una herramienta de linea de comandos para controlar el estado de los servicios y otras unidades.

Recargar los archivos de `systemd` para tu usuario.

```bash 
systemctl --user daemon-reload
```

Habilita y arranca el temporizador.

```bash 
systemctl --user enable sync-notes.timer 
systemctl --user start sync-notes.timer
```

Asegúrate que el servicio de usuario `systemd` se inicie al arrancar.

```bash 
sudo loginctl enable-linger $(whoami)
```

## Verificar que las units se ejecutan correctamente

Para `.service`

```bash 
systemctl --user status sync-notes.service
```

Para `.timer`

```bash 
systemctl --user status sync-notes.timer
```

## Ver los logs del sistema

Para ver los logs del servicio de usuario. 

```bash 
journalctl --user -u sync-notes.service
```

Ver los logs mas recientes y seguirlos en tiempo real.

```bash 
journalctl --user -u sync-notes.service -f
```

## Casos de uso

En mi situación particular, necesito sincronizar mis notas markdown con todos mis dispositivos (Laptop, PC de escritorio y teléfono) y poder utilizarlo en `Obsidian` y `nb`, mis principales aplicaciones de notas, por lo que un servicio ejecutándose de fondo resulta muy útil para mantener mi flujo de escritura.

<!-- TODO -->
<!-- - [ ] que son los cgroups? -->
<!-- - [ ] que son las unidades de destino? -->

