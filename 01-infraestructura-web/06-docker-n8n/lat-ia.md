# Despliegue de n8n Aislado en Docker (Ejemplo: Lat-IA)

Este es un caso de estudio real de despliegue de n8n para un flujo de IA en producción, utilizando Docker Compose, protegiéndolo con Nginx y aislándolo de la base de datos principal (PostgreSQL).

## Arquitectura de Aislamiento
n8n es una herramienta muy potente. Si se expone su puerto de forma incorrecta o consume toda la memoria del servidor, podría tirar la aplicación principal y la base de datos.

### El archivo `docker-compose.yml` (`/opt/lat-ia-n8n/docker-compose.yml`)

```yaml
version: '3.8'

services:
  n8n:
    image: n8nio/n8n
    restart: always
    ports:
      # CRÍTICO: Exponer el puerto ÚNICAMENTE a localhost (127.0.0.1)
      # Esto hace al contenedor inmune a accesos externos directos a la IP por el puerto 5678.
      # Solo Nginx (Reverse Proxy) podrá rutear tráfico hacia él.
      - "127.0.0.1:5678:5678"
    environment:
      - N8N_HOST=n8n.midominio.com
      - N8N_PORT=5678
      - N8N_PROTOCOL=https
      - NODE_ENV=production
      - WEBHOOK_URL=https://n8n.midominio.com/
      - GENERIC_TIMEZONE=America/Bogota
      # Mantenimiento Automático: Poda automática de datos de ejecución
      # Evita que la base de datos interna de n8n crezca infinitamente
      - EXECUTIONS_DATA_PRUNE=true
      - EXECUTIONS_DATA_MAX_AGE=48 # Horas
    volumes:
      - n8n_data:/home/node/.n8n
    deploy:
      resources:
        limits:
          # Límite duro de memoria para que n8n no compita con la App/PostgreSQL
          memory: 600M

volumes:
  n8n_data:
```

## Configuración DNS & Enrutamiento
1. En **Cloudflare**, crea un registro `A` apuntando `lat-ia` a la IP pública del VPS y actívale la "Nube Naranja" (Proxied) para ocultar la IP y manejar SSL.
2. En el servidor, configura Nginx para que escuche el subdominio y enrute internamente hacia el puerto `5678` aislado de Docker.

### Nginx Reverse Proxy (`/etc/nginx/sites-available/n8n.midominio.com`)

```nginx
server {
    listen 80;
    server_name n8n.midominio.com;

    location / {
        proxy_pass http://127.0.0.1:5678; # Hacia el contenedor Docker
        proxy_http_version 1.1;
        
        # WebSockets (Vital para la interfaz de n8n y flujos asíncronos)
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        
        # Headers del cliente original
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Streaming de Agentes LLM: Desactivar buffering
        proxy_buffering off;
        
        # Timeout agresivo (300s) para evitar cortes en peticiones lentas de IA
        proxy_read_timeout 300s;
        proxy_send_timeout 300s;
        proxy_connect_timeout 300s;
    }
}
```

Al utilizar este esquema, aprovechas Nginx y Cloudflare como un escudo y aseguras que n8n, al ser intensivo en I/O, no monopolice el disco ni la memoria del VPS.
