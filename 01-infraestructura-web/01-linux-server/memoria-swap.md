# Memoria Swap (Defensa ante Caídas)

Si tienes un servidor VPS con recursos limitados (ej. 1 GB o 2 GB de RAM) y levantas servicios pesados como bases de datos (PostgreSQL), contenedores Docker, o compilaciones de Node.js en producción, la RAM física puede saturarse. 

Cuando la RAM llega al 100%, el kernel de Linux ejecuta el proceso **OOM Killer** (Out Of Memory Killer), que mata procesos arbitrariamente para sobrevivir. Esto provoca caídas súbitas de tu API o Base de Datos.

Para mitigar esto, creamos un **archivo Swap**, que actúa como RAM virtual en el disco duro.

## Creación de un Archivo Swap de 2GB

```bash
# 1. Crear un archivo de 2 GB (2048 MB)
sudo fallocate -l 2G /swapfile

# 2. Asignar permisos estrictos (solo root puede leer/escribir)
sudo chmod 600 /swapfile

# 3. Formatear el archivo como memoria swap
sudo mkswap /swapfile

# 4. Activar el swap
sudo swapon /swapfile

# 5. Comprobar que está funcionando (Verás el swap disponible)
free -h
```

## Ajuste de Swappiness
El disco duro es muchísimo más lento que la RAM física. No queremos que el servidor empiece a usar el disco si aún le sobra RAM. Para ello, configuramos el parámetro `swappiness` al **10%** (por defecto suele venir en 60). Esto le dice al kernel: *"Usa el Swap solo cuando te quede el 10% o menos de RAM libre"*.

```bash
# Cambiarlo en tiempo real
sudo sysctl vm.swappiness=10
```

## Persistencia tras Reinicios
Para que este cambio (tanto el archivo Swap como el swappiness) sobreviva si reinicias el servidor VPS:

```bash
# 1. Añadir el swap a fstab (arranque)
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# 2. Hacer persistente el swappiness
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
```
