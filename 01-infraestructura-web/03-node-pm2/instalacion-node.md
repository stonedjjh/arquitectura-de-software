# Instalación de Node.js en Servidor

## Usando NVM (Recomendado)

```bash
# 1. Instalar NVM
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# 2. Recargar terminal y verificar
nvm --version

# 3. Instalar Node.js (ejemplo: Node 22 LTS)
nvm install 22

# 4. Verificar instalación
node -v
npm -v

# 5. Activar gestores de paquetes modernos (pnpm o yarn)
# Corepack viene incluido con Node.js y permite usar pnpm sin instalarlo globalmente
corepack enable
pnpm -v
```
