# Módulo 01: Infraestructura Web y Servidores

Este módulo engloba todos los conceptos, pasos y configuraciones necesarias para tomar un servidor en blanco (VPS), asegurarlo, y desplegar aplicaciones web y APIs listas para producción con las mejores prácticas de la industria.

## Flujo de Implementación Integral

Cuando provisionamos un nuevo servidor, este es el orden cronológico y conceptual que solemos seguir:

1. **`01-linux-server/` (Seguridad Perimetral y Sistema)**
   - Creación de usuario no root y desactivación del usuario root.
   - Forzar autenticación únicamente por **Llaves SSH** (y cómo configurarlo desde Windows con PuTTY).
   - Bloqueo de accesos directos configurando **Permisos Estrictos**.
   - Activación del Cortafuegos (**UFW**) y Prevención de Intrusiones (**Fail2ban**).
   - Defensa contra apagones por falta de RAM (**Memoria Swap**).

2. **`02-web-servers/` (Puerta de Enlace)**
   - Instalación de **Nginx**.
   - Configuración de Server Blocks.
   - Habilitación del **Proxy Inverso** para aplicaciones Node.js (Express, NestJS) y SPA (React Router).

3. **`03-node-pm2/` (Entorno de Ejecución Backend)**
   - Instalación correcta de la versión de Node LTS usando NVM.
   - Activación de gestores de paquetes modernos (`corepack`, `pnpm`).
   - Uso de **PM2** para mantener la aplicación corriendo en segundo plano y generar el script de auto-inicio ante reinicios (`systemd`).

4. **`04-cloudflare/` (Capa de Borde / CDN)**
   - Delegación de dominios y gestión de registros DNS.
   - Habilitación de encriptación de extremo a extremo (Origin Certificates) y proxy protector (Nube Naranja).

5. **`05-servicios-externos/` & `06-docker-n8n/` (Integraciones y Aislamiento)**
   - Testing seguro de emails transaccionales (Mailtrap).
   - Despliegues avanzados usando Docker (como automatizaciones n8n) bajo estrictos límites de memoria y aislamiento de red.

### ¿Cómo navegar este módulo?
Explora cada subcarpeta. En su interior encontrarás guías paso a paso (*Markdown*), *cheatsheets* y configuraciones listas para copiar, pegar y entender el **"por qué"** detrás de cada ajuste de arquitectura.
