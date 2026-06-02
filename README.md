# Documentación Colaborativa de Infraestructura LAMP para PYME

Documentación profesional y completa del despliegue de una infraestructura LAMP (Linux, Apache, MySQL, PHP) con monitorización, copias de seguridad y balanceador de carga HAProxy para una pequeña empresa (PYME).

## 📋 Descripción del Proyecto

Este repositorio contiene la documentación integral de un sistema de infraestructura empresarial que incluye:
- **Servidor Web**: Apache 2.4.60 con PHP 8.x
- **Base de Datos**: MySQL 8.0 (dos bases de datos independientes)
- **Balanceador de Carga**: HAProxy para distribución de tráfico
- **Seguridad**: SSH configurado, Firewall UFW, Fail2Ban
- **Monitorización**: Netdata para métricas en tiempo real
- **Backups**: Sistema automático de copias de seguridad con rsync
- **Recuperación**: Plan completo ante desastres (DR)

## 📚 Contenido de la Documentación

### Análisis y Diseño
- **[01 - Análisis de Requisitos](docs/01-analisis.md)**: Requisitos funcionales y no funcionales del cliente
- **[02 - Diseño de Infraestructura](docs/02-diseno.md)**: Diagrama de arquitectura, componentes, versiones y red
- **[03 - Planificación del Proyecto](docs/03-planificacion.md)**: Cronograma Gantt, fases, hitos y riesgos

### Instalación y Configuración
- **[Servidor Web Apache](docs/04-instalacion/servidor-web.md)**: Instalación de Apache 2.4 + PHP 8.x + SSL + HAProxy
- **[Base de Datos MySQL](docs/04-instalacion/base-de-datos.md)**: Instalación de MySQL 8.0 con usuarios y permisos
- **[SSH y Firewall](docs/04-instalacion/ssh-firewall.md)**: Configuración segura de acceso remoto y UFW
- **[Monitorización Netdata](docs/04-instalacion/monitorizacion.md)**: Sistema de alertas y métricas
- **[Copias de Seguridad](docs/04-instalacion/backups.md)**: Estrategia de backups automatizados

### Operación y Recuperación
- **[05 - Guía de Operación](docs/05-operacion.md)**: Tareas diarias, semanales y mensuales de mantenimiento
- **[06 - Plan de Recuperación ante Desastres](docs/06-recuperacion.md)**: Procedimientos para 5 escenarios de desastre

## 🛠️ Componentes del Sistema

| Componente | Versión | Función | Puerto |
|-----------|---------|---------|--------|
| Ubuntu | 22.04 LTS | Sistema Operativo | - |
| HAProxy | 2.8 | Balanceador de Carga | 80, 443 |
| Apache | 2.4.60 | Servidor Web | 8080, 8443 |
| PHP | 8.1 | Procesamiento Dinámico | - |
| MySQL | 8.0 | Base de Datos | 3306 |
| OpenSSH | 8.x | Acceso Remoto | 2222 |
| UFW | - | Firewall | - |
| Netdata | Última | Monitorización | 19999 |
| Fail2Ban | - | Protección SSH | - |

## 📊 Flujo de Tráfico

```
Cliente (Internet) 
  ↓ (HTTP/HTTPS puertos 80/443)
HAProxy (Balanceador)
  ↓ (Redirección puerto 8080/8443)
Apache (Servidor Web)
  ↓ (Consultas SQL)
MySQL (Base de Datos - localhost:3306)
```

## 🔒 Seguridad

- ✅ SSH en puerto personalizado (2222) con autenticación por clave pública
- ✅ Firewall UFW activo con reglas restrictivas
- ✅ Fail2Ban protegiendo contra ataques de fuerza bruta
- ✅ HTTPS con certificados Let's Encrypt
- ✅ MySQL sin acceso remoto (solo localhost)
- ✅ Separación de bases de datos (pública e interna)
- ✅ Actualizaciones automáticas de seguridad

## 📈 Monitorización

Sistema Netdata monitoreando:
- CPU, memoria, disco, red
- Servicios críticos (Apache, MySQL, SSH)
- Procesos principales
- Alertas automáticas por email/Slack

## 💾 Copias de Seguridad

- **Diarios**: 1 día de retención
- **Semanales**: 4 semanas de retención
- **Mensuales**: 6 meses de retención
- **Almacenamiento**: Local + remoto (NAS/Cloud)

## 📞 Contacto y Soporte

Para preguntas o contribuciones al proyecto, consultar:
- [Tareas del Proyecto](tareas.md)
- [Historial de Cambios](CHANGELOG.md)
- [Plan de Recuperación ante Desastres](docs/06-recuperacion.md)

## 📝 Versión

**v1.0** - Documentación completa con soporte para HAProxy y sistema de recuperación ante desastres

## 📅 Última Actualización

Junio 2026 - Sesión 4: Integración de HAProxy y finalización del proyecto

## 📋 Recursos Incluidos

- Diagramas Mermaid de arquitectura y flujos
- Scripts bash para automatización
- Configuraciones de ejemplo
- Checklists de implementación
- Procedimientos de troubleshooting
- Matriz de componentes y versiones
- Plan de comunicación y escalada
- Evaluación de riesgos

## 🎯 Próximos Pasos

1. Implementación en servidor de prueba
2. Validación con equipo IT
3. Capacitación del personal operativo
4. Migración a producción
5. Monitoreo intenso por 2 semanas
6. Actualización de documentación con lecciones aprendidas
