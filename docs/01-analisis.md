# 01 - Análisis de requisitos del cliente

## 1. Contexto general

La PYME necesita desplegar su primera infraestructura tecnológica para alojar su página web corporativa y un sistema interno de gestión. El equipo actuará como departamento de sistemas, documentando todo el proceso de diseño, planificación y operación.

El objetivo no es instalar los servicios, sino **documentar profesionalmente** cómo se haría, usando Markdown y GitHub.

---

## 2. Requisitos funcionales

### 2.1 Infraestructura web (LAMP)
- Servidor web **Apache 2.4.x**.
- Lenguaje de servidor **PHP 8.x**.
- Base de datos **MySQL/MariaDB**.
- Una base de datos para la web corporativa.
- Una base de datos para la aplicación interna.

### 2.2 Acceso remoto
- Acceso seguro mediante **SSH**.
- Autenticación por clave recomendada.
- Restricción de acceso mediante firewall.

### 2.3 Seguridad y red
- Firewall básico con **UFW**.
- Puertos permitidos:
  - 22/tcp (SSH)
  - 80/tcp (HTTP)
  - 443/tcp (HTTPS)
- Posible restricción de SSH a IPs de oficina.

### 2.4 Monitorización
- Herramienta ligera como **Netdata** o script personalizado.
- Supervisión de:
  - CPU
  - RAM
  - Disco
  - Servicios críticos (Apache, MySQL)

### 2.5 Copias de seguridad
- Backups automáticos:
  - `mysqldump` para bases de datos.
  - `rsync` para archivos.
- Rotación de copias.
- Programación con **cron**.

### 2.6 Documentación para el cliente
- Guía de mantenimiento.
- Plan de recuperación ante desastres.
- Diagramas, tablas y pasos claros.

---

## 3. Requisitos no funcionales

### 3.1 Sistema operativo
- **Ubuntu Server 22.04 LTS**.

### 3.2 Disponibilidad
- Infraestructura básica pero estable.
- Posible ampliación futura con balanceador HAProxy.

### 3.3 Escalabilidad
- Diseño modular para permitir:
  - Separación futura de servicios.
  - Balanceo de carga.
  - Réplicas de base de datos.

### 3.4 Mantenibilidad
- Documentación clara.
- Uso de GitHub con:
  - Ramas
  - Pull Requests
  - Revisiones
  - Resolución de conflictos

---

## 4. Requisitos de colaboración en GitHub

- Repositorio compartido entre dos miembros.
- Rama `main` protegida.
- PR obligatorios con al menos 1 aprobación.
- Uso de issues, ramas feature, merge y rebase.
- Generación de un release final.

---

## 5. Alcance del proyecto

El proyecto incluye:

- Diseño de infraestructura LAMP.
- Configuración de red y seguridad.
- Monitorización y backups.
- Documentación completa.
- Simulación de conflictos Git.
- Integración de un cambio de alcance: **balanceador HAProxy**.

No incluye:

- Instalación real de servidores.
- Despliegue en producción.
- Desarrollo de aplicaciones web.

---

## 6. Conclusión del análisis

La PYME necesita una infraestructura sencilla pero robusta, con buena documentación y prácticas profesionales de administración de sistemas. El proyecto permitirá simular un entorno real de trabajo colaborativo con Git y documentación técnica.
