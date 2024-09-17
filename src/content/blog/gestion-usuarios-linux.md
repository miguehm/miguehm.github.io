---
title: Gestionar usuarios en linux
author: Miguel Hernández
pubDatetime: 2024-09-17T12:59:10Z
slug: gestion-usuarios-linux
featured: false
draft: false
tags:
  - dev
  - linux
description: Gestionar usuarios desde la linea de comandos
---

## Contenidos

## Agregar un usuario

```bash
sudo adduser nombre_usuario
```

## Eliminar un usuario

Elimina el usuario.

```bash
sudo deluser nombre_usuario
```

Para eliminar también el directorio home del usuario:

```bash
sudo deluser --remove-home nombre_usuario
```

## Modificar un usuario

```bash
sudo usermod [OPCIONES] nombre_usuario
```

### Cambiar el nombre de usuario:

```bash
sudo usermod -l nuevo_nombre nombre_actual
```

### Cambiar el directorio home

```bash
sudo usermod -d /nueva/ruta nombre_usuario
```

### Cambiar el grupo principal

```bash
sudo usermod -g nuevo_grupo nombre_usuario
```

### Cambiar la contraseña de un usuario

```bash
sudo passwd nombre_usuario
```

### Bloquear o desbloquear un usuario

- Bloquear:
  ```bash
  sudo usermod -L nombre_usuario
  ```
- Desbloquear:
  ```bash
  sudo usermod -U nombre_usuario
  ```

## Ver los usuarios del sistema

```bash
cat /etc/passwd
```

## Agregar un usuario a un grupo

```bash
sudo usermod -aG nombre_grupo nombre_usuario
```

> La opción `-a` asegura que el usuario no sea eliminado de otros grupos a los que ya pertenezca.

## Eliminar un usuario de un grupo

```bash
sudo deluser nombre_usuario nombre_grupo
```
