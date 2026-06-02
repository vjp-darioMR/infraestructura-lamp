# Changelog

Todos los cambios significativos en este proyecto serán documentados en este archivo.

El formato se basa en [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/) y este proyecto se adhiere a [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0-rc1] - 2026-06-02

### Añadido
- **docs/05-operacion.md**: Redactada la guía de mantenimiento y operaciones diarias para el administrador de sistemas (Sesión 3).
- **docs/06-recuperacion.md**: Redactado el Plan de Recuperación ante Desastres (DRP) con RTO, RPO, flujos de trabajo en Mermaid, protocolos de resolución por escenario y guías paso a paso de restauración de copias de seguridad (Sesión 3).
- **docs/04-instalacion/monitorizacion.md**: Añadida la guía de instalación y configuración de Netdata, definición de métricas críticas a monitorizar, configuración de alertas personalizadas e integración de notificaciones por email (Sesión 2).
- **docs/04-instalacion/backups.md**: Redactada la estrategia de copias de seguridad de base de datos MySQL (con `mysqldump`) y archivos web (con `tar`), programados con `cron`, scripts de rotación automática, verificación de integridad y sincronización remota con `rsync` (Sesión 2).
- **docs/01-analisis.md**: Redactado el análisis detallado de requisitos funcionales y no funcionales del cliente (Sesión 1).
- **docs/02-diseno.md**: Redactada la arquitectura general de la infraestructura y el diagrama de red usando Mermaid (Sesión 1).
- **docs/03-planificacion.md**: Creado el esqueleto para la planificación temporal y fases del proyecto (Sesión 1).
- **docs/04-instalacion/servidor-web.md**, **base-de-datos.md**, **ssh-firewall.md**: Creado el esqueleto inicial para la documentación detallada de la instalación base (Sesión 1).

### Modificado
- **docs/02-diseno.md**: Integrada la actualización de versiones de software (Apache 2.4.60 y MySQL 8.0) y soporte SSL/TLS mediante Certbot (2.9) tras la resolución guiada de un conflicto de combinación git merge (Sesión 2).

### Documentado
- **README.md**: Actualizado con el índice de la documentación del proyecto y la estructura general del repositorio (Sesión 1).
