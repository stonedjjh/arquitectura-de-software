# Cloudflare: SSL y Reverse Proxy

Cloudflare actúa como un escudo (Proxy Inverso global) y Red de Distribución de Contenido (CDN) delante de nuestro servidor VPS. 

## Ventajas de usar Cloudflare con Nginx
1. **Oculta la IP de tu VPS:** Los atacantes solo ven las IPs de Cloudflare, reduciendo los ataques directos a tu servidor.
2. **Caché Global:** Entrega el contenido estático (imágenes, CSS, JS) desde servidores cercanos a tus usuarios.
3. **Gestión Automática de SSL:** Cloudflare maneja el certificado SSL de cara al usuario público sin costo y sin necesidad de renovarlo manualmente.

## Modos de Encriptación SSL (SSL/TLS Encryption modes)

En el panel de Cloudflare, bajo la sección de SSL/TLS, tienes varias opciones de cómo Cloudflare se conecta por detrás con tu servidor Nginx:

### 1. Flexible
- **Cómo funciona:** La conexión es segura (HTTPS) entre el usuario y Cloudflare, pero **no es segura** (HTTP por el puerto 80) entre Cloudflare y tu servidor Nginx.
- **Ventaja:** Nginx no requiere ninguna configuración de certificados SSL. Con tu bloque básico `listen 80;` es suficiente.
- **Desventaja:** El tráfico de red entre los servidores de Cloudflare y tu VPS viaja sin encriptar.

### 2. Full (Strict)
- **Cómo funciona:** La conexión es segura de extremo a extremo. Cloudflare exige que tu Nginx tenga un certificado SSL configurado y que escuche en el puerto 443.
- **Origin Certificates (Certificados de Origen):** No necesitas usar *Certbot* ni Let's Encrypt. En el panel de Cloudflare (sección *SSL/TLS -> Origin Server*) puedes generar un certificado gratuito exclusivo para la comunicación entre Cloudflare y tu servidor que **dura hasta 15 años**.
- **Ventaja:** Máxima seguridad y te olvidas de las renovaciones trimestrales de SSL en tu servidor local.

## Configuración de Nginx con "Origin Certificate" (Modo Full)

Una vez que generas tu certificado en Cloudflare, lo guardas en tu VPS (por ejemplo en `/etc/ssl/cloudflare/`), y actualizas tu Proxy Inverso de Nginx:

```nginx
server {
    listen 80;
    server_name tudominio.com www.tudominio.com;
    
    # Redirigir todo el tráfico HTTP a HTTPS
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name tudominio.com www.tudominio.com;

    # Rutas a los certificados de larga duración de Cloudflare
    ssl_certificate /etc/ssl/cloudflare/tudominio.com.pem;
    ssl_certificate_key /etc/ssl/cloudflare/tudominio.com.key;

    location / {
        proxy_pass http://localhost:3000; # Tu backend Node.js
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```
