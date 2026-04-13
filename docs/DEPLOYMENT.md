# Miliciano Deployment Guide

Miliciano se distribuye principalmente como CLI npm.

Objetivo del repo:
- publicar un paquete npm instalable
- arrancar un runtime Python local desde ese wrapper
- converger dependencias reales con `miliciano setup` y `miliciano bootstrap`

---

## Canal oficial de instalación

```bash
npm install -g @milytics/miliciano
miliciano setup --auto
miliciano status
```

Requisitos base:
- Linux
- Node.js 18+
- Python 3.10+
- npm
- curl

---

## Flujo recomendado

### 1. Instalar el paquete

```bash
npm install -g @milytics/miliciano
```

### 2. Convergencia inicial

```bash
miliciano setup --auto
```

Esto intenta:
- validar runtimes base
- reparar wrappers y PATH si hace falta
- preparar el runtime Python local
- revisar auth/rutas/proveedores principales

### 3. Diagnóstico

```bash
miliciano status
miliciano doctor
```

### 4. Reparación local

```bash
miliciano repair
```

Usa `repair` cuando:
- npm instaló el wrapper pero no encuentra Python
- cambió PATH
- quedaron enlaces o archivos locales fuera de sync

---

## Variables de entorno

Ver `.env.example` para opciones completas.

Variables comunes:

```bash
OPENAI_API_KEY=***
ANTHROPIC_API_KEY=***
OPENROUTER_API_KEY=***
GROQ_API_KEY=***
NEMOCLAW_POLICY_MODE=enforce
MILICIANO_DEBUG=0
OBSIDIAN_VAULT_PATH=~/Documents/Obsidian\ Vault
```

---

## Packaging npm

El artefacto publicado vive en `package.json` y expone:
- `bin/miliciano.js` como wrapper Node
- `miliciano-poc/bin/*` como runtime Python
- config base y documentación mínima del paquete

Verificación útil antes de publicar:

```bash
node scripts/verify-package.js
node bin/miliciano.js --help
python3 -m compileall bin miliciano-poc/bin
```

---

## Publicación

### Smoke local

```bash
npm test
```

### Publicar a npm

```bash
npm publish --access public
```

Antes de publicar:
- confirmar versión en `package.json`
- correr `node scripts/verify-package.js`
- validar que README refleje comandos reales

---

## Qué no es Miliciano hoy

Este repo no toma Docker como canal principal de instalación.

Por ahora el modelo correcto es:
- CLI personal / workstation tool
- instalación global por npm
- runtime local convergido en la máquina del usuario

Si más adelante aparece un caso real de runtime headless o self-hosted, esa ruta debe diseñarse como artefacto separado y con un proceso no interactivo explícito.

---

## Troubleshooting rápido

```bash
miliciano status
miliciano doctor
miliciano repair
```

Problemas típicos:
- wrapper npm no encuentra Python -> `miliciano repair`
- shell interactivo sin dependencias -> `miliciano setup`
- rutas/modelos desalineados -> `miliciano route sync`
- tooling local roto -> `miliciano tools health`
