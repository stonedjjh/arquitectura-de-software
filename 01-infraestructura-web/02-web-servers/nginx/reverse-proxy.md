# Nginx como Reverse Proxy

Un **Proxy Inverso (Reverse Proxy)** es un intermediario que se sitúa delante de uno o varios servidores de aplicaciones (backend) y se encarga de interceptar y gestionar todas las peticiones que llegan desde los clientes en Internet.

### ¿Por qué lo necesitamos con Node.js?
Por lo general, tu aplicación Node.js corre en puertos locales internos (como el `3000` o `5000`). No es seguro ni óptimo exponer esos puertos directamente a Internet. Además, por motivos de seguridad en Linux, los puertos por debajo del `1024` (como el `80` y `443`) requieren permisos de administrador (root), y correr una app Node.js como root es una mala práctica.

Al usar Nginx como Proxy Inverso logramos el siguiente flujo:
1. El usuario entra a `tudominio.com`. Nginx (que es robusto y seguro) intercepta la petición pública en el puerto `80` (HTTP) o `443` (HTTPS).
2. Nginx actúa como un puente y **redirige** internamente esa petición a tu aplicación Node.js en `http://localhost:3000`.
3. Node.js procesa la solicitud, genera el HTML/JSON y se lo devuelve a Nginx.
4. Nginx le entrega esa respuesta final al usuario.

## Configuración de Server Block
Archivo: `/etc/nginx/sites-available/tudominio.com`

```nginx
server {
    listen 80;
    # listen 443 ssl; # Habilitar para conexiones seguras HTTPS
    
    server_name tudominio.com www.tudominio.com;

    # (Las rutas a los certificados SSL normalmente las auto-configura Certbot)
    # ssl_certificate /etc/letsencrypt/live/tudominio.com/fullchain.pem;
    # ssl_certificate_key /etc/letsencrypt/live/tudominio.com/privkey.pem;

    location / {
        proxy_pass http://localhost:3000; # Puerto de tu app Node
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

## Habilitar el sitio
```bash
sudo ln -s /etc/nginx/sites-available/tudominio.com /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```
