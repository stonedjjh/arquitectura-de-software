# Instalación y Configuración Básica de Nginx

**Nginx** (pronunciado *"Engine-X"*) es un servidor web de código abierto, altamente escalable y de alto rendimiento. Es conocido mundialmente por su extrema estabilidad y su bajo consumo de recursos frente a servidores tradicionales como Apache.

### ¿Para qué se usa principalmente?
- **Servidor Web Estático:** Para despachar archivos HTML, CSS, imágenes y *bundles* de frontend (como los generados por React o Vue) a una velocidad increíble.
- **Proxy Inverso (Reverse Proxy):** Su caso de uso estrella en el desarrollo moderno. Nginx da la cara a Internet (puertos 80 y 443), recibe el tráfico y lo redirige internamente hacia aplicaciones que corren en otros puertos (por ejemplo, una API en Node.js en el puerto 3000).
- **Terminación SSL:** Se encarga de aplicar el cifrado HTTPS y leer los certificados de seguridad, quitándole esa carga a tu backend. **Ojo:** Nginx *no genera* los certificados. Estos son generados por entidades externas (Autoridades Certificadoras). La forma más común y gratuita es usar **Certbot (Let's Encrypt)**, un programa independiente que instalas en el servidor para que genere las llaves y se las entregue a Nginx.
- **Balanceador de Carga:** Para distribuir el tráfico entrante entre varios servidores si la aplicación crece.

## Instalación en Ubuntu/Debian
```bash
sudo apt update
sudo apt install nginx
```

## Comandos básicos
```bash
# Verificar estado
sudo systemctl status nginx

# Habilitar inicio automático
sudo systemctl enable nginx
```
