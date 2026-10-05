# Gestión de Dominios y DNS en Cloudflare

Antes de configurar proxies o certificados, es vital entender cómo funciona la delegación de dominios y cómo apuntar el tráfico hacia tu servidor VPS.

## Conceptos Básicos

1. **Dominio:** Es el nombre legible por humanos (ej. `midominio.com`) que el sistema DNS se encarga de traducir a la dirección IP real de tu servidor.
2. **Subdominio:** Es una extensión o prefijo del dominio principal (ej. `media.midominio.com` o `api.midominio.com`). Se usan muchísimo en arquitectura de software para aislar microservicios, APIs, o repositorios de *assets* separados del frontend.

## Tipos de Registros DNS Comunes

En el panel de Cloudflare (sección DNS), manejarás habitualmente estos registros:

- **Registro A (Address):** Apunta un nombre directamente a una **Dirección IP (IPv4)**. 
  *Ejemplo:* Creas un registro `A` para la raíz `@` que apunte a la IP `192.168.1.50` (la IP pública de tu VPS).
- **Registro AAAA:** Hace lo mismo que el registro A, pero apunta a una dirección IPv6.
- **Registro CNAME (Canonical Name):** Es un alias. Apunta un nombre hacia **otro nombre de dominio** en lugar de a una IP.
  *Ejemplo:* `www` -> `midominio.com`. O un subdominio de un microservicio `media.midominio.com` apuntando al dominio del servicio de almacenamiento en la nube.
- **Registro MX (Mail Exchange):** Le dice a Internet qué servidor se encarga de recibir los correos electrónicos de ese dominio.
- **Registro TXT:** Se usa para guardar información de texto. Generalmente para verificar la propiedad del dominio ante Google/AWS, o para reglas de seguridad de spam (SPF, DKIM).

---

## 🛠️ Caso de Uso Integral (Arquitectura de Producción)

A modo de *cheatsheet* de la vida real, así se ve una arquitectura madura combinando Nginx, UFW (Firewall) y Cloudflare:

### 1. Gestión de DNS
- **Registros A** apuntando a la IP pública del VPS (ej. `server1`).
- **Registros CNAME** delegados a subdominios específicos (ej. `media.midominio.com` utilizado por el microservicio de optimización de imágenes).

### 2. Proxy Inverso & CDN de Borde (Nube Naranja activa)
Al activar la nube naranja en los registros de Cloudflare, la plataforma se convierte en nuestro proxy inverso global:
- **Seguridad Perimetral:** Ocultamiento total de la IP real del VPS. Los escaneos maliciosos rebotan en Cloudflare y nunca llegan al servidor.
- **Performance:** Compresión automática en tránsito (Gzip / Brotli).
- **Caché de Borde (Edge Cache):** Tu servidor Nginx/Node.js puede enviar respuestas con políticas de caché inmutable (`Cache-Control: public, max-age=31536000, immutable`). Cloudflare lee y respeta estas cabeceras, guardando tus assets (imágenes, CSS, JS) en sus nodos de todo el mundo y sirviéndolos localmente a cada usuario sin volver a molestar a tu VPS.

### 3. Terminación SSL/TLS y Firewall
- **SSL:** Usamos el modo de cifrado SSL **Flexible** o **Full**. Esto permite ofrecer tráfico HTTPS en el puerto 443 hacia los clientes finales, sin la fricción de gestionar o renovar certificados manualmente en el servidor frontend.
- **Firewall (UFW):** Como el tráfico está protegido y canalizado por Cloudflare, a nivel de sistema operativo en el VPS configuramos `UFW` para cerrar absolutamente todo, exponiendo estrictamente solo 3 puertos: `22` (para nuestro acceso SSH), `80` (HTTP) y `443` (HTTPS).
