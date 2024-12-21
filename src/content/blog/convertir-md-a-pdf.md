---
title: Convertir markdown a pdf con grip y Firefox
author: Miguel Hernández
pubDatetime: 2024-12-21T14:20:21Z
slug: convertir-md-a-pdf
featured: false
draft: false
tags:
  - dev
  - markdown
  - pdf
description: Convertir documentos markdown a pdf legibles
---

## Grip

Renderiza markdown a html

```bash
grip -b doc.md
```

> `-b` abre el navegador para visualizar

## Inyectar CSS

Darle un estilo mas legible

```css
/* Page style */
body {
  font-family: "Arial", sans-serif;
  line-height: 1.6;
  background-color: #f4f4f4;
  color: #333;
  padding: 20px;
}

.page {
  max-width: 900px;
  margin: auto;
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

h1,
h2,
h3,
h4 {
  color: #333;
  font-weight: bold;
  margin-top: 20px;
}

h2 {
  border-bottom: 2px solid #eee;
  padding-bottom: 5px;
}

h3,
h4 {
  font-weight: normal;
}

p {
  margin: 10px 0;
  font-size: 16px;
  line-height: 1.8;
}

em {
  font-style: italic;
}

/* Links */
a {
  color: #0066cc;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}

/* Lists */
ul,
ol {
  margin-left: 20px;
}

ol {
  list-style-type: decimal;
}

ul {
  list-style-type: disc;
}

li {
  margin: 5px 0;
}

/* Blockquotes */
blockquote {
  padding: 10px 20px;
  margin: 20px 0;
  background-color: #f9f9f9;
  border-left: 4px solid #ccc;
  font-style: italic;
  color: #555;
}

/* Code block */
pre {
  background-color: #f0f0f0;
  padding: 15px;
  border-radius: 5px;
  overflow-x: auto;
  font-size: 14px;
  color: #333;
}

code {
  background-color: #f0f0f0;
  padding: 2px 5px;
  border-radius: 3px;
  font-size: 14px;
  color: #d14;
}

.highlight {
  background-color: #f0f0f0;
  padding: 10px;
  border-radius: 5px;
}

.highlight pre {
  background-color: transparent;
  border: none;
  color: #d14;
}

/* Tables */
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

th,
td {
  padding: 8px;
  border: 1px solid #ddd;
}

th {
  background-color: #f7f7f7;
  text-align: left;
}

/* Anchors for headers */
.anchor {
  display: none;
}

.heading-element {
  position: relative;
}

.heading-element:hover .anchor {
  display: inline-block;
  position: absolute;
  right: 0;
  top: 0;
  font-size: 18px;
  color: #0066cc;
}
```
