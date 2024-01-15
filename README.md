# <div align="center">
<p align="center">
<img src="images/banner.png">
</p>

# <div align="center">Instalar y configurar Fail2ban en OpenWrt

La instalación que se muestra a continuación es para <a href="https://openwrt.org/releases/23.05/notes-23.05.2" target="_blank">OpenWRT 23.5.2</a>.
  
**Versión a instalar:** <a href="https://openwrt.org/packages/pkgdata/fail2ban" target="_blank">Fail2ban 0.11.2-9</a>.

  ## Comandos: ##
  * Paso 1: Modificar el archivo /etc/config/system para hablitar la creación del archivo de log.
```bash 
vi /etc/config/system
```

  * Paso 2: Agregar las siguiente lineas.
```bash 
config system
        option log_size '128'
        option conloglevel '8'
        option cronloglevel '8'
        option log_file '/tmp/system.log'
```
Más info <a href="https://openwrt.org/docs/guide-user/base-system/system_configuration" target="_blank">aquí</a>.

  * Paso 3: Instalar Fail2ban.
```bash 
opkg update && opkg install fail2ban
```

  * Paso 4: Crear archivo jail.local (El archivo jail.conf no se debe modificar).
```bash 
cd /etc/fail2ban | touch jail.local
```

  * Paso 5: Agregar las siguientes lineas.
```bash 
vi /etc/fail2ban/jail.local
```

```bash 
[dropbear]
# Habilitar Fail2ban en Dropbear.
enabled = true

# Tiempo por el cual se bloqueara las ip.
bantime = 1d

# Indica el filtro que Fail2Ban utilizará para buscar patrones en los registros.
filter = dropbear

# Especifica la ruta del archivo de registro que Fail2Ban monitoreará.
logpath = /tmp/system.log

# Establece el número máximo de intentos fallidos permitidos antes de aplicar la acción definida.
maxretry = 2

# Utilizará iptables para bloquear el tráfico desde la dirección IP infractora en todos los puertos.
banaction = iptables-allports
```

  * Paso 6: Iniciar servicio y habilitar servicio con el inicio del sistema.
```bash 
/etc/init.d/fail2ban start
/etc/init.d/fail2ban enable
```

  ## Debug: ##
  * Ver que modulo esta activado.
 ```bash 
  fail2ban-client status
```
  * Ver estadisticas e ip bloqueadas.
 ```bash 
  fail2ban-client status dropbear
```
  * Desbloquear ip (Ej: Ip 192.168.1.10).
 ```bash 
  fail2ban-client set dropbear unbanip 192.168.1.10
```
  * Ver ip bloqueadas por Iptables.
 ```bash 
  iptables -L -n
```
  * Borrar historial
 ```bash 
  fail2ban-client restart dropbear
```

  
