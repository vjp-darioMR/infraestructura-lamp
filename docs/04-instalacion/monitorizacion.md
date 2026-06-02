# Instalación y Configuración de Monitorización

## 1. Introducción

Sistema de monitorización en tiempo real de la infraestructura LAMP usando Netdata, con capacidad de alertas y visualización de métricas.

### Arquitectura de Monitorización

```mermaid
graph TB
    subgraph SISTEMA["💻 Sistema a Monitorizar"]
        CPU["⚙️ CPU"]
        MEM["🧠 Memoria"]
        DISK["💾 Disco"]
        NET["🌐 Red"]
        PROC["📊 Procesos"]
    end
    
    subgraph NETDATA["📊 Netdata"]
        COLLECT["🔍 Recolección<br/>datos en tiempo<br/>real"]
        PROCESS["⚡ Procesamiento<br/>& análisis"]
        STORE["💾 Almacenamiento<br/>histórico"]
    end
    
    subgraph ALERTAS["🚨 Sistema de Alertas"]
        RULES["📋 Reglas<br/>de alerta"]
        EVAL["✅ Evaluación"]
        TRIGGER["⚡ Activación"]
    end
    
    subgraph NOTIF["📬 Notificaciones"]
        EMAIL["📧 Email"]
        SLACK["💬 Slack"]
        LOG["📝 Log"]
    end
    
    subgraph DISPLAY["🖥️ Visualización"]
        DASHBOARD["📈 Dashboard<br/>Web"]
        BROWSER["🌐 navegador<br/>Puerto 19999"]
    end
    
    CPU --> COLLECT
    MEM --> COLLECT
    DISK --> COLLECT
    NET --> COLLECT
    PROC --> COLLECT
    
    COLLECT --> PROCESS
    PROCESS --> STORE
    PROCESS --> RULES
    
    RULES --> EVAL
    EVAL --> TRIGGER
    
    TRIGGER --> EMAIL
    TRIGGER --> SLACK
    TRIGGER --> LOG
    
    STORE --> DASHBOARD
    DASHBOARD --> BROWSER
    
    style SISTEMA fill:#fff9c4
    style NETDATA fill:#c8e6c9
    style ALERTAS fill:#ffccbc
    style NOTIF fill:#f8bbd0
    style DISPLAY fill:#b3e5fc
    style COLLECT fill:#a5d6a7
    style PROCESS fill:#81c784
    style STORE fill:#66bb6a
    style RULES fill:#ffb74d
    style EVAL fill:#ff9800
    style TRIGGER fill:#ff6f00
    style DASHBOARD fill:#64b5f6
    style BROWSER fill:#1976d2
```

## 2. Requisitos

- Sistema operativo: Ubuntu Server 22.04 LTS
- Acceso root o permisos sudo
- Mínimo 200 MB de espacio en disco
- Conexión a Internet

## 3. Instalación de Netdata

### 3.1 Descargar e instalar
```bash
wget -O /tmp/netdata-kickstart.sh https://get.netdata.io/kickstart.sh
sh /tmp/netdata-kickstart.sh --stable-channel --disable-telemetry --install /opt
```

### 3.2 Alternativa con apt (en Ubuntu)
```bash
sudo apt install netdata -y
```

### 3.3 Verificar instalación
```bash
sudo systemctl status netdata
```

### 3.4 Habilitar al inicio
```bash
sudo systemctl enable netdata
```

## 4. Acceso a Netdata

### 4.1 URL de acceso
```
http://localhost:19999
http://192.168.1.100:19999  (desde otra máquina)
```

### 4.2 Configurar acceso remoto

Editar `/etc/netdata/netdata.conf`:

```bash
sudo nano /etc/netdata/netdata.conf
```

Buscar y modificar:
```ini
[web]
bind to = 0.0.0.0
port = 19999
```

## 5. Métricas Monitoreadas

Netdata monitorea automáticamente:

- **CPU**: Uso total, por núcleo
- **Memoria**: RAM, SWAP, página
- **Disco**: Lectura/escritura, espacio
- **Red**: Entrada/salida por interfaz
- **Procesos**: Número, top procesos
- **Sistema**: Load, interrupciones
- **Servicios**: Apache, MySQL, SSH

## 6. Configuración de Alertas

### 6.1 Editar archivo de alertas
```bash
sudo nano /etc/netdata/health.d/cpu.conf
```

### 6.2 Ejemplo de alerta de CPU

```yaml
alarm: cpu_usage
on: system.cpu
lookup: average -1m percentage
units: %
every: 1m
warn: $this > 80
crit: $this > 95
info: CPU usage alert
to: sysadmin
```

### 6.3 Reiniciar para aplicar cambios
```bash
sudo systemctl restart netdata
```

## 7. Integración con Notificaciones

### 7.1 Notificaciones por Email

Editar `/etc/netdata/health_alarm_notify.conf`:

```bash
sudo nano /etc/netdata/health_alarm_notify.conf
```

Configurar SMTP:
```bash
email_sender="netdata@miempresa.com"
smtp_server="mail.miempresa.com"
smtp_port=587
smtp_auth="yes"
smtp_username="usuario@miempresa.com"
smtp_password="contraseña"
```

## 8. Monitoreo de Servicios Críticos

| Servicio | Métrica | Alerta si |
|----------|---------|-----------|
| Apache | Procesos | < 1 |
| MySQL | Conexiones | 0 |
| SSH | Intentos fallidos | > 5 en 10 min |
| Sistema | CPU | > 80% en 5 min |
| Sistema | Memoria | < 10% disponible |
| Sistema | Disco | < 10% libre |

## 9. Troubleshooting

| Problema | Solución |
|----------|----------|
| Netdata no inicia | Revisar `/var/log/netdata/error.log` |
| No hay datos | Esperar 2-3 minutos para recolección |
| Alto uso CPU | Reducir frecuencia de actualización |
| Alerta no funciona | Verificar sintaxis en health.d |
| No se ve en red | Revisar firewall, puerto 19999 |

## 10. Mejores Prácticas

- ✓ Monitorear al menos 30 días de datos
- ✓ Configurar alertas realistas
- ✓ Revisar logs regularmente
- ✓ Mantener Netdata actualizado
- ✓ Hacer backup de configuración
- ✓ Probar alertas periódicamente
