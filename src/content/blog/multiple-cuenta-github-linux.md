---
title: Multiples Cuentas Github en Linux
author: Miguel Hernández
pubDatetime: 2025-06-30T11:25:00-06
slug: multiple-cuenta-github-linux
featured: true
draft: false
tags:
  - dev
  - git
  - github
  - linux
ogImage: ""
description: Tener multiples cuentas de github en terminal linux
canonicalURL: Multiple cuenta en github con linux.
---

# Generar Claves SSH distintas

Personal:

```bash
ssh-keygen -t ed25519 -C "tu_email_personal@example.com" -f ~/.ssh/id_ed25519_personal
```

Trabajo:

```bash
ssh-keygen -t ed25519 -C "tu_email_trabajo@example.com" -f ~/.ssh/id_ed25519_trabajo
```

# Añadir al Agente SSH

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_trabajo
```

# Configurar `~/.ssh/config`

```
# Cuenta personal
Host github.com-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal

# Cuenta del trabajo
Host github.com-trabajo
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_trabajo
```

# Uso

## Clonar repositorios

Usar el alias adecuado

```bash
git clone git@github.com-personal:usuario_personal/repositorio.git
```

# Nota

Configurar el `user.name` y `user.email` por repositorio

```bash 
git config user.name "Tu Nombre"
git config user.email "tu_email_correspondiente@example.com"
```
