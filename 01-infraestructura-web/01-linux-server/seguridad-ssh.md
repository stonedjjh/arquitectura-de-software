# Seguridad SSH

SSH (Secure Shell) es un protocolo criptográfico fundamental para la administración remota de servidores mediante la terminal. 

> [!WARNING]
> **Contexto de Seguridad:** En el momento en que un servidor se expone a Internet, se convierte en el blanco de *bots* automatizados que escanean IPs públicas 24/7. Estos scripts buscan puertos abiertos y ejecutan ataques de fuerza bruta o de diccionario sistemáticos para vulnerar credenciales débiles.

Para mitigar este riesgo casi por completo, la regla de oro es **deshabilitar la autenticación tradicional (usuario/contraseña)** y forzar un esquema basado exclusivamente en pares de claves criptográficas SSH. 

> [!TIP]
> **Mejores Prácticas:** Para entornos de producción críticos, es altamente recomendable cifrar la clave privada local con un *passphrase* y configurar autenticación de dos factores (2FA). Aquí documentaremos el esquema base de claves.

## Generación de Claves (En Local)
```bash
ssh-keygen -t ed25519 -C "tu_correo@ejemplo.com"
```

## Copiar Clave Pública al Servidor
Una vez generadas las claves, debemos copiar la clave pública (el archivo terminado en `.pub`) a nuestro servidor. Aunque el comando a veces la detecta por defecto, es buena práctica indicar explícitamente la ruta de la clave usando la bandera `-i`:
```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub nombre_usuario@ip_del_servidor
```

### ¿Dónde se guardan realmente estas claves? (`authorized_keys`)
El comando anterior automatiza un proceso muy simple: toma el texto de tu clave pública y lo añade como una nueva línea en el archivo `~/.ssh/authorized_keys` del usuario en el servidor remoto.

**Método Manual (Ideal para agregar múltiples usuarios):**
Si quieres dar acceso a otro desarrollador, solo pídele el texto de su clave pública y pégalo en una nueva línea dentro de este archivo:
```bash
# En el servidor (con el usuario al que quieres dar acceso)
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
```
*(Importante: Para que SSH no rechace la conexión por motivos de seguridad, la carpeta `.ssh` debe tener permisos `700` y el archivo `authorized_keys` `600`).*


## Hardening del Servidor SSH
Editar el archivo de configuración principal de SSH en el servidor:
```bash
sudo nano /etc/ssh/sshd_config
```

### 1. Cambiar el Puerto por Defecto
El puerto estándar de SSH es el `22`. Moverlo a un puerto no estándar (ej. entre `1024` y `65535`) no detiene ataques altamente dirigidos, pero **reduce drásticamente la "basura" en los logs** causada por escaneos básicos masivos.
```text
# Descomenta o cambia la directiva Port con tu número elegido
Port 2244
```

### 2. Bloquear Login de Root y Contraseñas
Dentro del **mismo archivo** (`/etc/ssh/sshd_config`), busca estas directivas y asegúrate de que tengan los siguientes valores para forzar el uso de llaves criptográficas y evitar que `root` se conecte directamente:
```text
PermitRootLogin no
PasswordAuthentication no
```
### 3. Aplicar los Cambios (Reiniciar SSH)
Para que todas las configuraciones de seguridad surtan efecto, debes reiniciar el servicio. 

> [!CAUTION]
> Antes de reiniciar, asegúrate de no cerrar tu terminal actual. Abre una **nueva pestaña** e intenta conectarte usando tu nuevo puerto para verificar que todo funciona correctamente y no quedarte afuera del servidor.

```bash
sudo systemctl restart ssh
```
