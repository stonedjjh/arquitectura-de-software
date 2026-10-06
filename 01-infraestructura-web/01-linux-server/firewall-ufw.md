# Cortafuegos (Firewall) y Fail2ban

En entornos de producción, cambiar el puerto SSH y aislar la red es fundamental, pero dejar puertos abiertos innecesariamente es un vector de ataque grave.

## UFW (Uncomplicated Firewall)
UFW es la interfaz por defecto en Ubuntu para gestionar `iptables`.

> [!WARNING]
> **Importante:** Si cambiaste tu puerto SSH (por ejemplo, al `2244`), **debes abrir este puerto en UFW antes de activarlo**. Si activas el firewall sin abrir el puerto SSH personalizado, ¡quedarás bloqueado fuera de tu propio servidor!

### Configuración Base Segura
La política por defecto debe ser siempre "Denegar todo lo que entra y permitir todo lo que sale".

```bash
# 1. Definir políticas por defecto
sudo ufw default deny incoming
sudo ufw default allow outgoing

# 2. Abrir el puerto SSH (Usa el tuyo personalizado, ej. 2244)
sudo ufw allow 2244/tcp comment 'SSH Custom'

# 3. Abrir puertos web estándar
sudo ufw allow 80/tcp comment 'HTTP'
sudo ufw allow 443/tcp comment 'HTTPS'

# 4. Habilitar el cortafuegos (Te pedirá confirmación)
sudo ufw enable

# 5. Comprobar el estado y las reglas aplicadas
sudo ufw status verbose
```

## Prevención de Intrusiones: Fail2ban
Aunque uses llaves SSH y hayas cambiado el puerto, los bots de Internet tarde o temprano encontrarán tu puerto abierto e intentarán miles de conexiones falsas para entrar.

**Fail2ban** es el estándar de la industria. Analiza continuamente los archivos de log del servidor (como los de SSH o Nginx) buscando intentos repetidos de autenticación fallidos y, si detecta abuso, **banea la dirección IP temporalmente** añadiendo una regla en el firewall.

### Instalación
```bash
sudo apt update
sudo apt install fail2ban
```
Con solo instalarlo y activarlo, Fail2ban por defecto comenzará a proteger el servicio SSH baneando IPs tras 5 intentos fallidos (por 10 minutos).

*(Nota: Si usas un puerto SSH no estándar, asegúrate de actualizar la configuración de la "jail" de `sshd` en `/etc/fail2ban/jail.local` para que escuche tu puerto específico).*
