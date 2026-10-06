# PM2 - Gestor de Procesos para Node.js

**PM2 (Process Manager 2)** es un gestor de procesos avanzado enfocado a entornos de producción para aplicaciones Node.js. 

### ¿Para qué sirve?
Normalmente, cuando ejecutas una app manualmente (`node index.js`), el proceso se queda atado a tu terminal. Si cierras tu conexión SSH, o si el código sufre un error no controlado (un *crash*), la aplicación se cae por completo y tus usuarios se quedan sin servicio.

PM2 ejecuta tu aplicación en **segundo plano (background)** y actúa como un sistema de vigilancia continua. Sus beneficios principales son:
- **Reinicio Automático (Auto-restart):** Si la app crashea por un error de código, PM2 la levanta de nuevo en milisegundos.
- **Persistencia (Startup):** Garantiza que tus aplicaciones arranquen solas automáticamente si el servidor Linux se reinicia.
- **Monitoreo:** Te permite vigilar el consumo de CPU, memoria RAM y leer los *logs* fácilmente.
- **Zero Downtime Reload:** Permite desplegar nuevo código reiniciando la app gradualmente sin desconectar a los usuarios activos.

## Instalación global
```bash
npm install pm2 -g
```

## Archivo `ecosystem.config.js`
Ejemplo de configuración para una app Node.js / TypeScript:

```javascript
module.exports = {
  apps : [{
    name   : "mi-app-web",
    script : "./dist/index.js", // Apunta al archivo compilado
    env_production: {
       NODE_ENV: "production",
       PORT: 3000
    }
  }]
}
```

## Comandos Comunes y Monitoreo

```bash
# 1. Iniciar la app usando el archivo de configuración
pm2 start ecosystem.config.js --env production

# 2. Ver QUÉ está corriendo (Lista de procesos, estado, memoria RAM y CPU)
pm2 ls
# (o también: pm2 status)

# 3. Monitorear procesos en tiempo real (Abre un dashboard interactivo)
pm2 monit

# 4. Revisar los logs (console.log y errores de la aplicación)
pm2 logs mi-app-web
pm2 logs mi-app-web --lines 100  # Ver solo las últimas 100 líneas

# 5. Reiniciar la app (Ideal tras subir cambios en el código)
pm2 restart mi-app-web
pm2 reload mi-app-web  # Recarga "suave" sin tirar las conexiones activas

> [!WARNING]
> **Cambios en el archivo `.env`:** Si modificaste tu archivo `.env` (ej: cambiaste credenciales de BD o API Keys), un `restart` normal **NO** aplicará las nuevas variables. PM2 guarda en memoria el entorno con el que arrancó la primera vez. Para forzar la lectura del nuevo `.env` debes ejecutar:
> ```bash
> pm2 restart mi-app-web --update-env
> ```

# 6. Detener o eliminar una app del gestor
pm2 stop mi-app-web
pm2 delete mi-app-web

# 7. Persistencia: Lograr que PM2 sobreviva si el VPS se reinicia (corte eléctrico, mantenimiento)
# Paso A: Generar el script de integración con el sistema de arranque (systemd)
pm2 startup systemd
# IMPORTANTE: PM2 imprimirá un comando en consola (empieza con "sudo env PATH..."). 
# Debes copiar y pegar ese comando y ejecutarlo.

# Paso B: Guardar el estado actual de los procesos para que se auto-inicien
pm2 save
```
