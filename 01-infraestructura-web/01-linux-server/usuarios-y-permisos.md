# Usuarios y Permisos en Linux

## Creación de Usuario no-root
```bash
# 1. Crear nuevo usuario
adduser nombre_usuario

# 2. Agregar el usuario al grupo sudo
usermod -aG sudo nombre_usuario
```

## Bloqueo de la cuenta Root (Seguridad)
Por seguridad, una vez tengas tu usuario `sudo` configurado y hayas validado que puedes conectarte por SSH con él, bloquea la contraseña del usuario root para evitar ataques directos:
```bash
sudo passwd -l root
```

## Configuración de Sudoers sin Contraseña (Scripts de Despliegue)
En entornos de CI/CD (como GitHub Actions) o si usas scripts de despliegue automatizado, a veces necesitas que el usuario ejecute comandos de root (como reiniciar PM2 o Nginx) sin que la terminal le pida contraseña.

Para hacerlo de forma segura, edita el archivo de *sudoers* usando:
```bash
sudo visudo
```
Al final del archivo, puedes agregar una regla para tu usuario. (Ejemplo: permitir recargar Nginx sin password):
```text
nombre_usuario ALL=(ALL) NOPASSWD: /usr/sbin/nginx -s reload, /usr/bin/pm2 reload all
```

## Permisos Básicos (Web Root)
Cuando despliegas una aplicación (como una SPA en `/var/www/miapp`), asegúrate de que el usuario de despliegue y Nginx tengan acceso a los archivos. Nginx generalmente usa el usuario `www-data`.

**Cambiar propiedad:**
```bash
sudo chown -R tu_usuario:www-data /var/www/miapp
```

**Permisos seguros (Directorios a 755 y Archivos a 644):**
```bash
sudo find /var/www/miapp -type d -exec chmod 755 {} \;
sudo find /var/www/miapp -type f -exec chmod 644 {} \;
```
