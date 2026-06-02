# Instalación y Configuración de Base de Datos

## 1. Introducción

Documentación completa de instalación y configuración de MySQL 8.0 para alojar dos bases de datos independientes: una pública (sitio web) y otra interna (gestión).

## 2. Requisitos Previos

- Sistema operativo: Ubuntu Server 22.04 LTS
- Acceso root o permisos sudo
- Aproximadamente 1 GB de espacio en disco
- Conexión a Internet

## 3. Instalación de MySQL

### 3.1 Actualizar repositorio
```bash
sudo apt update
```

### 3.2 Instalar MySQL
```bash
sudo apt install mysql-server mysql-client -y
```

### 3.3 Ejecutar instalación segura
```bash
sudo mysql_secure_installation
```

Responder a las preguntas:
- ¿Instalar plugin VALIDATE PASSWORD? → Sí (recomendado)
- Nivel de validación → 2 (fuerte)
- Cambiar contraseña root → Sí
- Eliminar usuarios anónimos → Sí
- Deshabilitar login remoto root → Sí
- Eliminar base de datos test → Sí
- Recargar tablas de privilegios → Sí

### 3.4 Verificar instalación
```bash
mysql --version
sudo systemctl status mysql
```

### 3.5 Habilitar al inicio
```bash
sudo systemctl enable mysql
sudo systemctl start mysql
```

## 4. Configuración Inicial

### 4.1 Acceder a MySQL
```bash
sudo mysql -u root -p
# O sin contraseña en instalación reciente:
sudo mysql
```

### 4.2 Crear usuarios
```sql
-- Usuario para sitio web público
CREATE USER 'usuario_web'@'localhost' IDENTIFIED BY 'contraseña_fuerte_123';

-- Usuario para sistema interno
CREATE USER 'usuario_interno'@'localhost' IDENTIFIED BY 'contraseña_fuerte_456';

-- Usuario administrador
CREATE USER 'admin'@'localhost' IDENTIFIED BY 'contraseña_admin_789';
```

### 4.3 Crear bases de datos
```sql
-- Base de datos pública
CREATE DATABASE db_sitio_web CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- Base de datos interna
CREATE DATABASE db_gestion_interna CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

## 5. Asignar Permisos

### 5.1 Permisos para usuario web
```sql
GRANT SELECT, INSERT, UPDATE, DELETE ON db_sitio_web.* TO 'usuario_web'@'localhost';
FLUSH PRIVILEGES;
```

### 5.2 Permisos para usuario interno
```sql
GRANT ALL PRIVILEGES ON db_gestion_interna.* TO 'usuario_interno'@'localhost';
FLUSH PRIVILEGES;
```

### 5.3 Permisos para administrador
```sql
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

### 5.4 Ver permisos asignados
```sql
SHOW GRANTS FOR 'usuario_web'@'localhost';
SHOW GRANTS FOR 'usuario_interno'@'localhost';
SHOW GRANTS FOR 'admin'@'localhost';
```

## 6. Configuración de Seguridad

### 6.1 Editar archivo de configuración
```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Añadir o modificar:
```ini
# Seguridad
bind-address = 127.0.0.1
skip-external-locking = 1
max_connections = 100
default-storage-engine = InnoDB

# Performance
innodb_buffer_pool_size = 256M
max_allowed_packet = 256M
query_cache_type = 0
```

### 6.2 Reiniciar MySQL
```bash
sudo systemctl restart mysql
```

### 6.3 Cambiar puerto (opcional, más seguro)
```ini
port = 3307  # Cambiar de 3306 a otro puerto
```

## 7. Pruebas de Conexión

### 7.1 Probar usuario web
```bash
mysql -u usuario_web -p db_sitio_web
# Ingresar contraseña: contraseña_fuerte_123
```

### 7.2 Crear tabla de prueba
```sql
CREATE TABLE usuarios (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    fecha_registro TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 7.3 Insertar datos de prueba
```sql
INSERT INTO usuarios (nombre, email) VALUES 
('Juan Pérez', 'juan@miempresa.com'),
('María García', 'maria@miempresa.com'),
('Carlos López', 'carlos@miempresa.com');
```

### 7.4 Consultar datos
```sql
SELECT * FROM usuarios;
```

## 8. Conexión desde PHP

### 8.1 Crear archivo config.php
```php
<?php
// Configuración de base de datos
define('DB_HOST', 'localhost');
define('DB_USER', 'usuario_web');
define('DB_PASS', 'contraseña_fuerte_123');
define('DB_NAME', 'db_sitio_web');

// Crear conexión
$conexion = new mysqli(DB_HOST, DB_USER, DB_PASS, DB_NAME);

// Verificar conexión
if ($conexion->connect_error) {
    die("Conexión fallida: " . $conexion->connect_error);
}

// Establecer charset
$conexion->set_charset("utf8mb4");

// Opcional: mostrar errores durante desarrollo
mysqli_report(MYSQLI_REPORT_ERROR | MYSQLI_REPORT_STRICT);
?>
```

### 8.2 Probar conexión
```php
<?php
include 'config.php';

$sql = "SELECT * FROM usuarios";
$result = $conexion->query($sql);

if ($result->num_rows > 0) {
    while($row = $result->fetch_assoc()) {
        echo "ID: " . $row["id"] . " - Nombre: " . $row["nombre"] . "<br>";
    }
} else {
    echo "No hay registros";
}
?>
```

## 9. Mantenimiento de Base de Datos

### 9.1 Optimizar tablas
```sql
OPTIMIZE TABLE usuarios;
OPTIMIZE TABLE db_sitio_web.*;
```

### 9.2 Verificar integridad
```sql
CHECK TABLE usuarios;
```

### 9.3 Reparar tabla (si está dañada)
```sql
REPAIR TABLE usuarios;
```

### 9.4 Estadísticas de uso
```bash
# Tamaño de base de datos
mysql -u admin -p -e "SELECT table_name, ROUND(((data_length + index_length) / 1024 / 1024), 2) as 'Size in MB' FROM information_schema.TABLES WHERE table_schema = 'db_sitio_web';"

# Ver procesos
mysql -u admin -p -e "SHOW PROCESSLIST;"
```

## 10. Información de Conexión

| Parámetro | Valor |
|-----------|-------|
| Host | localhost |
| Puerto | 3306 |
| Usuario Web | usuario_web |
| Usuario Interno | usuario_interno |
| Usuario Admin | admin |
| DB Pública | db_sitio_web |
| DB Interna | db_gestion_interna |
| Charset | utf8mb4 |

## 11. Seguridad: NO HACER

```php
// ❌ NUNCA hacer esto - INSEGURO
$password = "123456";  // Contraseña débil
$conexion = new mysqli("localhost", "root", "");  // Root sin contraseña
$sql = "SELECT * FROM usuarios WHERE id=" . $_GET['id'];  // SQL Injection
```

## 12. Seguridad: HACER

```php
// ✓ CORRECTO - Usar prepared statements
$stmt = $conexion->prepare("SELECT * FROM usuarios WHERE id = ?");
$stmt->bind_param("i", $id);
$id = $_GET['id'];
$stmt->execute();
$result = $stmt->get_result();
```

## 13. Solución de Problemas

| Problema | Solución |
|----------|----------|
| "Access denied for user" | Verificar usuario, contraseña, host |
| "Unknown database" | Verificar nombre de BD con SHOW DATABASES; |
| "No connection possible" | Verificar bind-address en mysqld.cnf |
| Tabla llena | Ampliar innodb_buffer_pool_size |
| Conexión lenta | Verificar índices, usar EXPLAIN |
| Derechos insuficientes | Revisar GRANT con SHOW GRANTS |

## 14. Checklista de Configuración

- ✓ MySQL instalado y habilitado
- ✓ Instalación segura completada
- ✓ Dos bases de datos creadas
- ✓ Tres usuarios con permisos específicos
- ✓ Configuración de seguridad aplicada
- ✓ Conexión desde PHP funciona
- ✓ Backups programados
- ✓ Conexión remota deshabilitada
- ✓ Puerto 3306 no expuesto a internet
- ✓ Charset utf8mb4 configurado
