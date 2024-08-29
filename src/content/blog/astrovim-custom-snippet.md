---
title: Configurar snippets personalizados en Astrovim 
author: Miguel Hernández Moreno
pubDatetime: 2024-08-28T20:01:35Z
slug: snippets-custom-astrovim
featured: true
draft: false
tags:
  - dev
  - tool
  - astrovim
  - neovim
ogImage: ""
description: Agregar snippets personalizados a Astrovim con lua-snip
---

Un snippet es un fragmento de código o texto predefinido que se puede insertar rápidamente en un archivo.

> `lua-snip` ya viene instalado en AstroVim por defecto

## Contenidos

## Crear archivo de configuración

Crear `luasnip.lua` en `.config/nvim/lua/plugins`

```lua
return {
  "L3MON4D3/LuaSnip",
  config = function(plugin, opts)
    -- include the default astronvim config that calls the setup call
    require "astronvim.plugins.configs.luasnip"(plugin, opts)
    -- load snippets paths
    require("luasnip.loaders.from_vscode").lazy_load {
      paths = { vim.fn.stdpath "config" .. "/snippets" },
    }
  end,
}
```

## Crear carpeta de snippets y los JSON

Crear carpeta `snippets` en `.config/nvim`

Un ejemplo en caso de markdown:

```json
{
  "setup": {
    "prefix": ["saludo", "hola"],
    "body": [
      "Hola!",
      "Como te va?",
      "R: ${1}",
      "",
      "${0}"
    ],
    "description": "Inserta un saludo"
  }
}
```

- `${1}` Permite colocar el cursor acorde al orden establecido.
- `${0}` será la última posición.

Crear `package.json` en `.config/nvim`

```json
{
  "name": "user_snippets",
  "engines": {
    "vscode": "^1.11.0"
  },
  "contributes": {
    "snippets": [
      {
        "language": "markdown",
        "path": "./snippets/markdown.json"
      }
    ]
  }
}
```

La jerarquía desde `.config/nvim/` debe quedar de la siguiente manera:

```bash
.
├── lua
│   ├── plugins
│   │   ├── luasnip.lua
├── package.json
└── snippets
    └── markdown.json
```

<!-- #dev/tool #astrovim #nvim #pending -->
