# 02 - Diseño de la infraestructura

## 1. Arquitectura general

La infraestructura propuesta sigue un modelo clásico **LAMP** sobre un único servidor Linux, con posibilidad de ampliación futura mediante un balanceador HAProxy.

Componentes principales:

- Servidor Ubuntu 22.04 LTS.
- Apache + PHP para la web.
- MySQL/MariaDB para bases de datos.
- Firewall UFW.
- Acceso SSH seguro.
- Monitorización ligera.
- Sistema de backups automatizado.

---

## 2. Diagrama de red (Mermaid)

```mermaid
flowchart LR
    A[Usuario externo] -->|HTTP/HTTPS| B[Servidor Apache<br>Ubuntu 22.04]
    B -->|Consultas SQL| C[(MySQL/MariaDB)]
    D[Administrador] -->|SSH| B
    B -->|Métricas| E[Monitorización<br>Netdata]
    B -->|Backups| F[Servidor de copias<br>rsync / almacenamiento]
