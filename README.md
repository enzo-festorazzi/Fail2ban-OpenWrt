# <div align="center">
<p align="center">
<img src="images/banner.png">
</p>

# <div align="center">Instalar y configurar Fail2ban en OpenWrt

La instalación que se muestra a continuación es para <a href="https://openwrt.org/releases/23.05/notes-23.05.4" target="_blank">OpenWRT 23.5.4</a>.
  
**Versión a instalar:** <a href="https://openwrt.org/packages/pkgdata/fail2ban" target="_blank">Fail2ban 0.11.2-8</a>.

  ## Configuración de archivo de log: ##
  * Paso 1: Modificar el archivo /etc/config/system para hablitar la creación del archivo de log.
```bash 
vi /etc/config/system
```
  * Paso 2: Reiniciar servicio de log.
```bash 
service log restart
```

  * Paso 3: Agregar las siguiente lineas.
```bash 
config system
        option log_size '128'
        option conloglevel '8'
        option cronloglevel '8'
        option log_file '/tmp/system.log'
```
Más info <a href="https://openwrt.org/docs/guide-user/base-system/system_configuration" target="_blank">aquí</a>.

  ## Instalación: ##

  * Paso 4: Instalar Fail2ban.
```bash 
opkg update && opkg install fail2ban
```

  * Paso 5: Crear archivo jail.local (El archivo jail.conf no se debe modificar).
```bash 
cd /etc/fail2ban | touch jail.local
```

  * Paso 6: Agregar las siguientes lineas.
```bash 
vi /etc/fail2ban/jail.local
```

```bash 
[DEFAULT]
# Tiempo por el cual se boqueara las ip. -1 Quedara bloqueada por siempre.
bantime = -1

[dropbear]
# Habilitar Fail2ban en Dropbear.
enabled = true

# Indica el filtro que Fail2Ban utilizará para buscar patrones en los registros.
filter = dropbear

# Especifica la ruta del archivo de registro que Fail2Ban monitoreará.
logpath = /tmp/system.log

# Establece el número máximo de intentos fallidos permitidos antes de aplicar la acción definida.
maxretry = 1

# Utilizará iptables para bloquear el tráfico desde la dirección IP infractora en todos los puertos.
banaction = iptables-allports
```

  * Paso 7: Cambiar ubicacion de historial de ip bloqueadas para que se guarden de forma permanente.

Crear carpeta
```bash
mkdir /Fail2ban
```
Abrir archivo de configuración
```bash
vi /etc/config/fail2ban
```
Valores a configurar
```bash
config fail2ban 'fail2ban'
        option dbfile '/Fail2ban/fail2ban.sqlite3'
```


  * Paso 8: Iniciar servicio y habilitar servicio con el inicio del sistema.
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

  
