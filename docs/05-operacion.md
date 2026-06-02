# 05 - Guía de Operación y Mantenimiento Diario

Este documento sirve como manual operativo para el administrador de sistemas de la PYME. Detalla los procedimientos rutinarios necesarios para garantizar la estabilidad, rendimiento y seguridad de la infraestructura LAMP.

---

## 1. Calendario de Tareas de Mantenimiento

Para mantener el sistema en un estado óptimo, el administrador debe seguir un calendario estructurado de revisiones:

| Frecuencia | Tarea | Componente | Herramienta / Método |
| :--- | :--- | :--- | :--- |
| **Diaria** | Monitoreo del estado de servicios | Apache, MySQL, SSH | Netdata / `systemctl` |
| **Diaria** | Comprobación de espacio en disco | Sistema de archivos | `df -h` / Alerta Netdata |
| **Diaria** | Verificación de ejecución de backups | MySQL y Archivos Web | Lectura de `/home/backups/*/logs/` |
| **Semanal** | Actualización de parches de seguridad | Sistema Operativo | `apt update && apt upgrade` |
| **Semanal** | Inspección de accesos SSH y Firewall | Seguridad | `ufw status` / `last` / `journalctl` |
| **Mensual** | Prueba de restauración de backups | Copias de seguridad | Simulación de recuperación |
| **Mensual** | Auditoría y optimización de base de datos | MySQL | `mysqlcheck` / `OPTIMIZE TABLE` |
| **Mensual** | Rotación y análisis de logs antiguos | Apache, MySQL, System | `logrotate` / `ncdu` |

---

## 2. Monitorización del Sistema con Netdata

El servidor cuenta con **Netdata** para la monitorización en tiempo real. La interfaz es accesible desde el navegador web de la red local:

*   **URL de acceso local**: `http://localhost:19999`
*   **URL de acceso remoto**: `http://<IP_DEL_SERVIDOR>:19999`

### 2.1 Métricas Críticas a Vigilar

El administrador debe comprobar diariamente el panel principal de Netdata, prestando especial atención a:

1.  **CPU (system.cpu)**: El uso medio debe estar por debajo del **70%**. Si supera el **80%** de forma sostenida (más de 5 minutos), se debe identificar el proceso causante con el comando `top` o `htop`.
2.  **Memoria (system.ram)**: Monitorear el consumo de RAM física y Swap. Si el uso de RAM supera el **90%** y el sistema empieza a paginar activamente en SWAP, puede indicar una fuga de memoria o la necesidad de reajustar los límites de Apache/MySQL.
3.  **Disco (disk.space)**: La partición raíz `/` debe tener al menos un **15%** de espacio libre. Si baja del **10%**, Netdata disparará una alerta crítica.
4.  **Servicios Activos**:
    *   **Apache**: Comprobar el volumen de peticiones por segundo y el número de procesos activos.
    *   **MySQL**: Comprobar las conexiones activas y el volumen de consultas (queries/s).

### 2.2 Gestión de Alertas

Netdata está configurado para enviar alertas automáticas por correo electrónico o logs locales ante anomalías. Si se activa una alerta:
*   Las alertas se pueden inspeccionar visualmente en la pestaña **Alarms** en la esquina superior derecha del dashboard de Netdata.
*   En caso de alerta crítica por correo, comprobar los detalles en `/var/log/netdata/error.log`.

---

## 3. Control de Copias de Seguridad (Backups)

La seguridad de los datos depende de la correcta ejecución de los backups programados en `/home/backups/`.

### 3.1 Verificación de Ejecución Diaria

Cada mañana, el administrador debe validar que las tareas programadas en el `cron` se hayan ejecutado correctamente a las **02:00 AM** (MySQL) y **03:00 AM** (Web) revisando los logs:

```bash
# Verificar logs de backup de Base de Datos
cat /home/backups/mysql/logs/backup.log

# Verificar logs de backup de archivos Web
cat /home/backups/www/logs/backup.log

# Verificar logs de sincronización remota Rsync
cat /home/backups/logs/rsync.log
```

Un log correcto debe mostrar una salida similar a:
`Backup completado: 20260602_020000`

### 3.2 Comprobación de Almacenamiento y Espacio

El comando `df -h` permite confirmar que el volumen de backups no está saturando el disco:

```bash
df -h /home/backups
```

### 3.3 Scripts de Verificación de Integridad

No basta con que el archivo exista; debe ser legible y libre de corrupción. Se debe ejecutar semanalmente el script `/usr/local/bin/verificar-backups.sh` (detallado en [backups.md](file:///c:/Users/Systemm32/Desktop/D/CLASE/infraestructura-lamp/docs/04-instalacion/backups.md)):

```bash
sudo /usr/local/bin/verificar-backups.sh
```

> [!IMPORTANT]
> Si el script devuelve un mensaje de `ERROR` para algún archivo `.tar.gz` o `.sql.gz`, la copia de ese día debe considerarse corrupta. Se debe ejecutar un backup manual inmediatamente usando los scripts correspondientes y revisar los logs de hardware en `/var/log/syslog`.

---

## 4. Mantenimiento del Servidor Web Apache y PHP

### 4.1 Comandos de Administración del Servicio
```bash
# Comprobar estado del servicio
sudo systemctl status apache2

# Reiniciar de manera segura (hace un reload sin cortar conexiones activas)
sudo systemctl reload apache2

# Forzar reinicio completo
sudo systemctl restart apache2
```

### 4.2 Revisión y Rotación de Logs
Los archivos de registro se encuentran en `/var/log/apache2/`.
*   `error.log`: Registra fallos del servidor, advertencias de PHP y errores de configuración. Debe revisarse regularmente para diagnosticar páginas caídas o scripts defectuosos:
    ```bash
    sudo tail -n 50 /var/log/apache2/error.log
    ```
*   `access.log`: Registra todas las peticiones HTTP/HTTPS entrantes. Útil para auditoría y detección de patrones de tráfico anormales.

La rotación automática está configurada mediante el servicio `logrotate`. Se puede verificar que se ejecuta correctamente comprobando la existencia de archivos comprimidos históricos:
```bash
ls -la /var/log/apache2/
# Deberían listarse archivos como error.log.1, error.log.2.gz, etc.
```

---

## 5. Mantenimiento del Servidor de Base de Datos MySQL

### 5.1 Comandos de Administración del Servicio
```bash
# Comprobar estado del motor de base de datos
sudo systemctl status mysql

# Reiniciar el servicio
sudo systemctl restart mysql
```

### 5.2 Diagnóstico y Optimización de Tablas
Con el tiempo, las bases de datos pueden fragmentarse o acumular índices ineficientes. Se recomienda ejecutar mensualmente una verificación y optimización de las bases de datos del sistema (`web_corporativa` e `intranet`):

```bash
# Verificar todas las bases de datos
sudo mysqlcheck -u admin -p --all-databases --check

# Optimizar tablas fragmentadas
sudo mysqlcheck -u admin -p --all-databases --optimize
```

### 5.3 Revisión del Log de Errores de MySQL
Cualquier fallo en la escritura de datos, bloqueos de tablas o caídas inesperadas quedará registrado en:
```bash
sudo tail -n 50 /var/log/mysql/error.log
```

---

## 6. Seguridad y Actualizaciones del Sistema

### 6.1 Procedimiento de Actualización de Paquetes
Para garantizar que el servidor cuente con los últimos parches de seguridad para Apache, PHP, MySQL y el propio Kernel de Ubuntu Server 22.04 LTS:

```bash
# 1. Actualizar el índice de paquetes
sudo apt update

# 2. Listar paquetes actualizables (para revisión visual previa)
apt list --upgradable

# 3. Aplicar las actualizaciones de seguridad de forma segura
sudo apt upgrade -y
```

> [!WARNING]
> Después de una actualización crítica de paquetes como Apache (`apache2`) o MySQL (`mysql-server`), se debe verificar inmediatamente la disponibilidad de la web corporativa y el panel interno de gestión para descartar problemas de compatibilidad de configuración.

### 6.2 Comprobación de Seguridad (Firewall y SSH)
Es vital auditar semanalmente quién accede al servidor y si los puertos están correctamente protegidos:

```bash
# 1. Comprobar que UFW está activo y con las reglas LAMP correctas
sudo ufw status verbose
# Salida esperada: 22/tcp (SSH), 80/tcp (HTTP) y 443/tcp (HTTPS) permitidos

# 2. Listar últimos inicios de sesión SSH exitosos
last -n 10 -a -F

# 3. Monitorizar intentos fallidos de conexión SSH (posibles ataques de fuerza bruta)
sudo journalctl -u ssh | grep "Failed password" | tail -n 20
```

Si se detectan ataques de fuerza bruta masivos desde IPs específicas, se recomienda bloquearlas manualmente con UFW:
```bash
sudo ufw deny from <IP_ATACANTE> to any
```
o configurar una herramienta de protección activa como `fail2ban`.

---

## 7. Mantenimiento del Balanceador de Carga (HAProxy)

Con la integración de **HAProxy** como puerta de enlace del tráfico HTTP en el puerto `80`, es necesario incorporar su mantenimiento en las rutinas operativas del servidor.

### 7.1 Control del Servicio
```bash
# Comprobar el estado del balanceador
sudo systemctl status haproxy

# Recargar configuración en caliente (seguro para entornos en producción)
sudo systemctl reload haproxy

# Reiniciar completamente el servicio
sudo systemctl restart haproxy
```

### 7.2 Validación de Configuración
Antes de realizar cualquier recarga o reinicio del servicio de balanceo, es obligatorio validar la sintaxis del archivo `/etc/haproxy/haproxy.cfg`:
```bash
haproxy -c -f /etc/haproxy/haproxy.cfg
# Salida correcta: Configuration file is valid
```

### 7.3 Monitorización y Estadísticas en Tiempo Real
Para facilitar el monitoreo del balanceador, se ha habilitado un panel gráfico de estadísticas en el puerto `9000`.

1.  **Habilitar puerto en el Firewall**:
    ```bash
    sudo ufw allow 9000/tcp
    ```
2.  **Acceso al Dashboard**:
    Navegar a `http://<IP_DEL_SERVIDOR>:9000/` e introducir las credenciales de administración configuradas en `/etc/haproxy/haproxy.cfg` (ej. `admin` / `AdminStats123!`).
3.  **Métricas clave en el panel**:
    *   **Queue**: Número de peticiones esperando en cola si el backend de Apache está saturado.
    *   **Session Rate**: Número de sesiones por segundo.
    *   **Status**: Indica en verde (`UP`) si el backend de Apache responde correctamente al check de salud HTTP, o en rojo (`DOWN`) en caso contrario.

### 7.4 Análisis de Logs y Troubleshooting
Los eventos de conexión y errores de HAProxy se envían al syslog del sistema:
```bash
# Ver peticiones entrantes y errores del balanceador en tiempo real
sudo tail -f /var/log/haproxy.log
# O de manera general con journalctl
sudo journalctl -u haproxy -n 50
```

#### Solución al error `503 Service Unavailable`
Si los usuarios informan de un error `503` al intentar entrar al sitio web:
1.  **Comprobar que HAProxy está corriendo**: `sudo systemctl status haproxy`.
2.  **Verificar que Apache está activo en el puerto 8080**:
    ```bash
    sudo ss -tlpn | grep :8080
    ```
    Si no devuelve salida, significa que Apache está caído. Inícielo con `sudo systemctl start apache2`.
3.  **Comprobar el log de HAProxy**: Si muestra mensajes como `Server apache_backend/web-apache is DOWN`, verifique que la IP y el puerto de Apache en `/etc/haproxy/haproxy.cfg` coinciden exactamente con la configuración de `/etc/apache2/ports.conf`.

