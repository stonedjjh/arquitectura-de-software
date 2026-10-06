# Instalación y Configuración de PostgreSQL en Ubuntu

## 1. Instalación Rápida
PostgreSQL es robusto y excelente para producción. Puedes instalarlo junto a Redis (muy usado para caché en APIs y colas de tareas) con este comando:

```bash
sudo apt update
sudo apt install -y postgresql postgresql-contrib redis-server
sudo systemctl enable postgresql redis-server
sudo systemctl start postgresql redis-server
```

## 2. Script: Creación Automática de Base de Datos y Usuario
En lugar de entrar a la consola interactiva `psql` y escribir sentencias manualmente, puedes usar este flujo en bash para generar una contraseña súper segura, crear la BD, el usuario, darle permisos, y **guardar la cadena de conexión directamente en tu archivo `.env`**.

*(Ejecuta esto en el directorio donde esté tu proyecto backend)*:

```bash
# 1. Generar contraseña aleatoria de 32 caracteres
DB_PASS=$(openssl rand -hex 16)

# 2. Crear base de datos y usuario
sudo -u postgres psql -c "CREATE DATABASE miapp;"
sudo -u postgres psql -c "CREATE USER miapp_user WITH ENCRYPTED PASSWORD '$DB_PASS';"

# 3. Otorgar permisos
sudo -u postgres psql -c "GRANT ALL PRIVILEGES ON DATABASE miapp TO miapp_user;"
sudo -u postgres psql -d miapp -c "GRANT ALL ON SCHEMA public TO miapp_user;"

# 4. Guardar la URI de conexión en tu .env
echo "DATABASE_URL=\"postgresql://miapp_user:${DB_PASS}@localhost:5432/miapp\"" >> .env

# Opcional: Proteger tu archivo .env
chmod 600 .env

echo "¡Base de datos configurada con éxito!"
```

## 3. Comandos de Diagnóstico
Si necesitas verificar el estado o inyectar datos (como el seed que ejecutaste):

- **Listar todas las bases de datos:** `sudo -u postgres psql -c "\l"`
- **Ver las tablas de tu BD:** `sudo -u postgres psql -d miapp -c "\dt"`
- **Ejecutar migraciones puras (.sql):** `sudo -u postgres psql -d miapp -f ruta/a/migracion.sql`
