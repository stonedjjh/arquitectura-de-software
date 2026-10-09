# Gestión de Dominios y DNS en Cloudflare

> [!IMPORTANT]
> Para los siguientes ejemplos de configuración de registros, supondremos que la IP pública de tu VPS es `192.168.1.50`. Recuerda sustituirla por tu IP real al configurar tu dominio.

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

## 🔗 El Flujo Universal de Vinculación: Dominio ➔ VPS

Este es un paso clave que muchos tutoriales omiten: cómo vincular un dominio recién comprado (ej. Namecheap) con tu máquina VPS, pasando por Cloudflare como puente central.

```text
[ Registrador: Namecheap ] ──(Nameservers)──> [ DNS: Cloudflare ] ──(Registro A)──> [ VPS: Ubuntu ]
  (Compra del dominio)                         (Tráfico y SSL)                       (Tu Máquina)
```

### Paso 1: En el Registrador de Dominio (ej. Namecheap)
Por defecto, al comprar un dominio, este apunta a los servidores DNS básicos del registrador. Debemos delegar esa responsabilidad.
1. Entra a la lista de tus dominios y busca la sección **Nameservers** (Servidores de Nombres).
2. Cambia la opción por defecto a **Custom DNS**.
3. Coloca los Nameservers exactos que te asigna Cloudflare (ej: `ns1.cloudflare.com` y `ns2.cloudflare.com`).
4. Guarda el cambio.
*¿Qué lograste?* Le dijiste a Internet: *"Cualquiera que pregunte por mi dominio, pregúntenle a Cloudflare, ellos saben cómo llegar"*.

### Paso 2: En el Panel del VPS (Proveedor de Hosting)
1. Ubica y copia la **IP Pública** de tu servidor (ej: `203.0.113.50`).
2. Configura el **Hostname** de la máquina (ej: `server1.midominio.com`).
3. *(Opcional)* Si tu proveedor tiene un campo "Attach Domain", complétalo para que su sistema interno de soporte sepa a qué proyecto pertenece ese VPS.

### Paso 3: En Cloudflare (El Puente Definitivo)
Aquí es donde atas todo el tráfico hacia tu IP real de manera segura.
1. En Cloudflare, vas a la pestaña **DNS ➔ Records**.
2. **Registro A raíz (`@` o `midominio.com`):**
   - Type: `A`
   - Name: `@`
   - IPv4 address: `203.0.113.50`
   - Proxy status: **Proxied (Nube Naranja)** *(Para protección DDoS, ocultar IP real y aplicar SSL).*
3. **Registro CNAME para `www`:**
   - Type: `CNAME`
   - Name: `www`
   - Target: `midominio.com`
   - Proxy status: **Proxied (Nube Naranja)**
4. **Registro A para conexión al servidor (SSH):**
   - Type: `A`
   - Name: `server1`
   - IPv4 address: `203.0.113.50`
   - Proxy status: **DNS only (Nube Gris)** *(Crucial: La nube debe estar gris para que puedas conectar por SSH/PuTTY a `server1.midominio.com` sin que Cloudflare bloquee tu puerto personalizado).*

> [!TIP]
> **Escalabilidad Centralizada:** Documentar esto vale oro. Cuando tengas más clientes o servidores, el Servidor 1 tendrá la IP `.193` (Tienda Principal) y el Servidor 2 tendrá otra IP `.50` (Microservicio). **Nunca más tendrás que tocar el Registrador.** Todo se administrará desde una sola pantalla en Cloudflare añadiendo Registros `A` hacia las IPs respectivas.

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
