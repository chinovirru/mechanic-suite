# Mechanic Suite - Mecanica Alvaro

Sistema de gestión para taller mecánico con dashboard React y API Laravel.

## 🏗️ Arquitectura

Este proyecto utiliza una arquitectura **monorepo** con Docker para facilitar el desarrollo:

```
mechanic-suite/
├── frontend/           # React Dashboard (Mantis Material UI)
├── backend/site/       # Laravel 12 API
├── docker-compose.yml  # Orquestación de servicios
└── docker/            # Configuraciones Docker
```

## 🚀 Stack Tecnológico

### Frontend
- **React 19** con Material UI v7
- **Vite 7** - Build tool rápido
- **React Router v7** - Navegación
- **Mantis Dashboard** v1.6.0 - Template profesional
- **SWR** - Data fetching
- **Formik + Yup** - Manejo de formularios

### Backend
- **Laravel 12** (PHP 8.3)
- **Apache 2.4**
- **MySQL 8.4**
- Migraciones automáticas al inicio

### Infraestructura
- **Docker** + **Docker Compose**
- **Yarn 4.9.1** (Corepack)
- **Composer 2.x**

## 📦 Servicios Docker

| Servicio | Puerto | Descripción |
|----------|--------|-------------|
| `frontend` | 3030 → 5173 | Dashboard React con Vite dev server |
| `backend` | 80 | API Laravel con Apache |
| `mysql` | 3307 → 3306 | Base de datos MySQL 8.4 |

## 🎯 Inicio Rápido

### Prerrequisitos
- Docker Desktop instalado
- Git

### Instalación

1. **Clonar el repositorio**
```bash
git clone <repository-url>
cd mechanic-suite
```

2. **Iniciar servicios**
```bash
docker-compose up -d
```

Esto automáticamente:
- Construye las imágenes Docker
- Instala dependencias (npm/composer)
- Configura permisos
- Ejecuta migraciones
- Inicia todos los servicios

3. **Acceder a las aplicaciones**
- **Frontend**: http://localhost:3030
- **Backend API**: http://localhost:80
- **MySQL**: localhost:3307

## 🗄️ Configuración de Base de Datos

### Credenciales MySQL

```env
Host: localhost (o 127.0.0.1)
Port: 3307
Database: mechanic
Username: root
Password: root_password
```

### Conexión desde cliente GUI
Puedes usar MySQL Workbench, DBeaver, HeidiSQL, etc. con las credenciales anteriores.

**Nota**: MySQL 8.4 usa `caching_sha2_password`. Asegúrate de que tu cliente lo soporte.

## 🛠️ Comandos Útiles

### Docker

```bash
# Iniciar todos los servicios
docker-compose up -d

# Ver logs en tiempo real
docker-compose logs -f

# Ver logs de un servicio específico
docker logs mechanic-frontend
docker logs mechanic-backend
docker logs mechanic-mysql

# Detener todos los servicios
docker-compose down

# Reconstruir servicios
docker-compose up -d --build

# Reiniciar un servicio específico
docker-compose restart frontend
```

### Frontend (dentro del contenedor)

```bash
# Entrar al contenedor
docker exec -it mechanic-frontend sh

# Instalar dependencias
yarn install

# Linting
yarn lint
yarn lint:fix

# Formateo de código
yarn prettier
```

### Backend (dentro del contenedor)

```bash
# Entrar al contenedor
docker exec -it mechanic-backend sh

# Comandos Artisan
php artisan migrate
php artisan tinker
php artisan route:list

# Composer
composer install
composer require <package>
```

### MySQL

```bash
# Acceder desde línea de comandos
mysql -h 127.0.0.1 -P 3307 -uroot -proot_password mechanic

# Backup de base de datos
docker exec mechanic-mysql mysqldump -uroot -proot_password mechanic > backup.sql

# Restaurar backup
mysql -h 127.0.0.1 -P 3307 -uroot -proot_password mechanic < backup.sql
```

## 📁 Estructura del Proyecto

### Frontend (`/frontend`)
```
frontend/
├── src/
│   ├── components/      # Componentes reutilizables
│   ├── layout/          # Layouts (Dashboard, Auth)
│   ├── pages/           # Páginas de la aplicación
│   ├── routes/          # Configuración de rutas
│   ├── themes/          # Temas y estilos MUI
│   └── utils/           # Utilidades
├── Dockerfile
├── package.json
└── vite.config.mjs
```

### Backend (`/backend/site`)
```
backend/site/
├── app/
│   ├── Http/
│   │   └── Controllers/
│   └── Models/
├── database/
│   ├── migrations/
│   └── seeders/
├── routes/
├── public/              # DocumentRoot de Apache
└── storage/             # Logs, cache, uploads
```

## 🔧 Configuración

### Variables de Entorno

#### Backend (`.env`)
El archivo `.env` de Laravel está en `backend/site/.env`:

```env
APP_NAME="Mecanica Alvaro"
APP_ENV=local
APP_DEBUG=true

DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=mechanic
DB_USERNAME=root
DB_PASSWORD=root_password
```

**Importante**: El `.env` está en `.gitignore` y no se commitea. Usa `.env.example` como referencia.

#### Frontend (Vite)
Las variables de entorno deben tener prefijo `VITE_`:

```env
VITE_APP_BASE_NAME=/
VITE_APP_VERSION=v1.6.0
```

## 🐛 Solución de Problemas

### Frontend no carga
```bash
# Verificar logs
docker logs mechanic-frontend

# Reiniciar servicio
docker-compose restart frontend

# Reconstruir
docker-compose up -d --build frontend
```

### Backend retorna 500
```bash
# Verificar permisos
docker exec mechanic-backend ls -la /app/site/storage

# Ver logs de Laravel
docker exec mechanic-backend tail -f /app/site/storage/logs/laravel.log

# Ver logs de Apache
docker exec mechanic-backend tail -f /var/log/apache2/error.log
```

### No puedo conectarme a MySQL
- Verifica que el puerto 3307 esté libre: `netstat -an | grep 3307`
- Asegúrate de usar `127.0.0.1` en vez de `localhost` si tienes problemas
- Verifica que tu cliente soporte `caching_sha2_password`

### Permisos de archivos en Windows
Si tienes problemas con permisos (especialmente en storage/):
```bash
docker exec mechanic-backend chown -R www-data:www-data /app/site/storage /app/site/bootstrap/cache
docker exec mechanic-backend chmod -R 775 /app/site/storage /app/site/bootstrap/cache
```

## 🔐 Seguridad

### Producción
Antes de llevar a producción:

1. **Cambiar credenciales**
   - Generar nuevo `APP_KEY`: `php artisan key:generate`
   - Cambiar contraseña de MySQL
   - Configurar `APP_ENV=production` y `APP_DEBUG=false`

2. **HTTPS**
   - Configurar certificados SSL
   - Actualizar Apache VirtualHost

3. **Base de datos**
   - Crear usuario específico (no usar root)
   - Restringir acceso por IP

## 📝 Git y Desarrollo

### Estructura de Branches
- `main` - Código en producción
- `develop` - Desarrollo activo
- `feature/*` - Nuevas funcionalidades
- `fix/*` - Corrección de bugs

### Commits
Este proyecto sigue [Conventional Commits](https://www.conventionalcommits.org/):

```bash
feat: Nueva funcionalidad
fix: Corrección de bug
docs: Cambios en documentación
style: Formateo, punto y coma faltante, etc.
refactor: Refactorización de código
test: Agregar tests
chore: Tareas de mantenimiento
```

## 📚 Documentación Adicional

- [CLAUDE.md](./CLAUDE.md) - Guía para Claude Code AI
- [Frontend README](./frontend/README.md) - Documentación específica de React
- [Backend README](./backend/site/README.md) - Documentación de Laravel

## 🤝 Contribución

1. Hacer fork del proyecto
2. Crear una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit de cambios (`git commit -m 'feat: Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abrir Pull Request

## 📄 Licencia

Este proyecto es privado y propietario de Mecanica Alvaro.

## 👥 Equipo

- **Proyecto**: Mecanica Alvaro
- **Frontend Template**: Mantis Free React (CodedThemes)
- **Backend Framework**: Laravel

## 🆘 Soporte

Para problemas o preguntas:
1. Revisar la sección de **Solución de Problemas**
2. Verificar logs de Docker
3. Consultar documentación de [Laravel](https://laravel.com/docs) y [React](https://react.dev)

---

**Última actualización**: Octubre 2025
