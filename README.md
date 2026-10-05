# Mis Guías DevOps & Arquitectura Web

![DevOps](https://img.shields.io/badge/DevOps-Cheatsheet-blue) ![Linux](https://img.shields.io/badge/Linux-Server-orange) ![Nginx](https://img.shields.io/badge/Nginx-Proxy-green) ![Node.js](https://img.shields.io/badge/Node.js-PM2-success)

¡Bienvenido a mi repositorio personal de conocimiento! Aquí documento mis configuraciones, arquitecturas y "cheatsheets" para despliegues en producción. Este repositorio está pensado para ser una referencia rápida y viva a lo largo de mi carrera como Ingeniero de Software / Arquitecto.

## Propósito

Este proyecto no es una aplicación, sino una **Base de Conocimiento Centralizada**. El objetivo es:
- Estandarizar mis procesos de despliegue.
- No depender de la memoria para configuraciones críticas (SSH, Nginx, PM2, etc.).
- Facilitar la adopción rápida de nuevas herramientas tecnológicas.

## Índice de Contenidos

### I. Infraestructura Web y DevOps (`/01-infraestructura-web`)

**1. Servidor VPS y Seguridad Linux**
- [Usuarios y Permisos (sudoers)](01-infraestructura-web/01-linux-server/usuarios-y-permisos.md)
- [Acceso SSH Seguro sin contraseñas](01-infraestructura-web/01-linux-server/seguridad-ssh.md)

**2. Servidores Web y Proxies**
- [Instalación y Configuración Básica de Nginx](01-infraestructura-web/02-web-servers/nginx/instalacion-basica.md)
- [Nginx como Reverse Proxy (Node.js)](01-infraestructura-web/02-web-servers/nginx/reverse-proxy.md)

**3. Node.js y PM2**
- [Instalación de Node.js en Servidor](01-infraestructura-web/03-node-pm2/instalacion-node.md)
- [Gestión de procesos con PM2 (ecosystem.config.js)](01-infraestructura-web/03-node-pm2/pm2-ecosystem.md)

**4. DNS y CDN (Cloudflare)**
- [Gestión de Dominios y Registros DNS](01-infraestructura-web/04-cloudflare/gestion-dns.md)
- [Configuración de SSL y Proxies en Cloudflare](01-infraestructura-web/04-cloudflare/ssl-proxy.md)

### II. Arquitectura de Software (`/02-arquitectura-software`)
*(Espacio reservado para futuras notas sobre Patrones de Diseño, Clean Architecture, Microservicios, etc.)*

### III. Bases de Datos (`/03-bases-de-datos`)
*(Espacio reservado para notas de Modelado de Datos, SQL, NoSQL, Redis, etc.)*

### Otros Repositorios Relacionados
Temas más extensos o altamente especializados los mantengo en repositorios dedicados:
- ![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white) **[Notas de Docker](https://github.com/stonedjjh/notas_docker)**: Referencia para contenedores, imágenes y Docker Compose.
- ![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat&logo=github-actions&logoColor=white) **[GitHub Actions](https://github.com/stonedjjh/github-actions)**: Pipelines de CI/CD y automatización de despliegues.
- ![Patrones](https://img.shields.io/badge/Patrones_de_Diseño-4CAF50?style=flat) **[Patrones de Diseño](https://github.com/stonedjjh/patrones-diseno)**: Referencia y ejemplos prácticos.
- ![SOLID](https://img.shields.io/badge/SOLID_%26_Clean_Code-00599C?style=flat) **[SOLID & Clean Architecture](https://github.com/stonedjjh/solid-clean)**: Principios de arquitectura.
- ![GCP](https://img.shields.io/badge/Google_Cloud-4285F4?style=flat&logo=google-cloud&logoColor=white) **[Google Cloud Platform](https://github.com/stonedjjh/Google-Cloud-Platform)**: Prácticas de arquitectura Cloud, Terraform y laboratorios de certificación.

## Cómo usar esta guía

1. **Navegación:** Usa los enlaces del índice de arriba para saltar directamente al manual que necesites.
2. **Actualización Constante:** Cada vez que resuelvas un problema nuevo o configures un entorno distinto, agrega una nueva guía o edita las existentes.
3. **Scripts:** En la carpeta `/scripts` se guardarán scripts y comandos útiles de bash/python para automatización.

## Contacto

**Daniel Jimenez**
- **GitHub:** [https://github.com/stonedjjh](https://github.com/stonedjjh)
- **LinkedIn:** [https://www.linkedin.com/in/daniel-jimenez-88a2a293/](https://www.linkedin.com/in/daniel-jimenez-88a2a293/)
