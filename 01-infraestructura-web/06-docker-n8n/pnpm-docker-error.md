# Docker y PNPM: El Error de Scripts Ignorados (`ERR_PNPM_IGNORED_BUILDS`)

Cuando intentas dockerizar aplicaciones modernas de Node.js (usando Vite, Next.js, NestJS, etc.) y utilizas **pnpm** como gestor de paquetes (especialmente versiones `> 9.0`), es muy común que el paso de construcción en el `Dockerfile` falle abruptamente al ejecutar `pnpm install --frozen-lockfile`.

## El Problema Real
Verás un error similar a este en los logs de construcción de tu contenedor:
```text
Error: ERR_PNPM_IGNORED_BUILDS
× installing dependencies
╰─▶ Ignored build scripts: esbuild@0.28.2, sharp@0.33.5
help: Run "pnpm approve-builds" to pick which dependencies should be allowed to run scripts.
```

### ¿Por qué ocurre?
Por seguridad frente a ataques a la cadena de suministro, las versiones recientes de `pnpm` bloquean la ejecución automática de scripts `postinstall` de paquetes de terceros. Librerías pesadas orientadas a binarios C++ como **esbuild** (el motor detrás de Vite) o **sharp** (el motor de compresión de imágenes) necesitan compilar o descargar binarios nativos específicos del sistema operativo durante la instalación. Al estar bloqueadas por pnpm, la instalación fracasa.

## La Solución en tu Dockerfile

Dado que el contenedor Docker ya es un entorno temporal y aislado, la solución más pragmática para CI/CD es desactivar esta restricción antes de ejecutar el comando de instalación.

Añade `pnpm config set ignore-scripts false` justo antes de instalar las dependencias:

```dockerfile
# (Pasos previos de tu Dockerfile...)
# Instalar pnpm (ejemplo activando corepack)
RUN corepack enable && corepack prepare pnpm@latest --activate

# Copiar manifiestos
COPY package.json pnpm-lock.yaml ./

# IMPORTANTE: Desactivar bloqueo de scripts para que 'esbuild' y 'sharp' puedan compilarse
RUN pnpm config set ignore-scripts false

# Ahora sí, instalar dependencias
RUN pnpm install --frozen-lockfile
```

> [!NOTE]
> Otra alternativa más estricta (útil para desarrollo local, no tanto en Docker) es correr `pnpm approve-builds` manualmente en tu máquina, lo que guardará en el `package.json` un bloque `"pnpm": { "onlyBuiltDependencies": ["esbuild", "sharp"] }`. Sin embargo, esto requiere mantenimiento cada vez que instalas un paquete nativo nuevo.
