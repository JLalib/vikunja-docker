# 📋 Vikunja Docker - Task Manager Autohospedado

[![GitHub](https://img.shields.io/badge/GitHub-vikunja%2Fvikunja-blue?logo=github)](https://github.com/vikunja/vikunja)
[![Docker](https://img.shields.io/badge/Docker-vikunja%2Fvikunja-blue?logo=docker)](https://hub.docker.com/r/vikunja/vikunja)
[![License](https://img.shields.io/badge/License-AGPL--3.0-orange)](https://github.com/vikunja/vikunja/blob/main/LICENSE)

## 📋 Descripción general

**Vikunja** es un gestor de tareas completamente autohospedado, open source y multi-plataforma construido con **Go (backend)** + **Vue.js (frontend)**. Proporciona múltiples vistas (kanban boards, Gantt charts, calendar, list view), colaboración en equipo (teams, projects, namespaces), **CalDAV provider** (sincroniza con Thunderbird, Outlook, iCal), API REST completa (OpenAPI docs), Webhooks para integraciones, import/export iCal, subtasks, labels, attachments, notificaciones, due dates, reminders, recurring tasks, múltiples bases de datos (SQLite, MySQL, PostgreSQL), autenticación multi-usuario, sin vendor lock-in, completamente privado. **13k+ GitHub stars**, **AGPL-3.0 open source**, **Docker ready**, **production-ready**, escalable horizontalmente.

## ✨ Características principales

- **Kanban boards** - Drag-drop tasks, custom columns, swimlanes, card templates
- **Gantt charts** - Timeline visualization, task dependencies, milestones, critical path
- **Calendar view** - Day/week/month, due dates, reminders, multi-task days
- **Teams & Projects** - Multiple teams, namespaces, projects, sharing permissions granular
- **CalDAV provider** - Sync con Thunderbird, Outlook, iCal, two-way sync, standards
- **REST API** - OpenAPI docs, full API, Webhooks, custom integrations
- **Subtasks** - Task hierarchy, nested subtasks, progress tracking, dependencies
- **Labels & Tags** - Custom labels, color coding, filtering, multi-select
- **Attachments** - Upload files, images, documents, in-task access
- **Notifications** - Email alerts, in-app notifications, customizable, subscriptions
- **Recurring tasks** - Recurring patterns (daily, weekly, monthly, custom), auto-create
- **Comments & Activity** - Task discussion, activity log, mentions, @ notifications
- **Multi-database** - SQLite (embedded), MySQL 5.7+, PostgreSQL 10+
- **Multi-platform** - Linux, Windows, macOS | Multi-arch: amd64, arm64, armv7

## 📋 Requisitos del sistema

- **Docker & Docker Compose v2+**
- **RAM**: 512 MB - 2 GB mínimo (recomendado 2GB+ para equipos grandes)
- **Disco**: 1 GB - 50GB+ espacio (según datos tareas)
- **Puerto TCP**: 3456 (web UI, configurable)
- **Database**: SQLite (embedded) OR MySQL 5.7+ OR PostgreSQL 10+
- **Multi-plataforma**: Linux, Windows, macOS
- **Multi-arch**: amd64, arm64, armv7
- **Opcional**: Reverse proxy nginx/Caddy para HTTPS + dominio
- ⚠️ **AGPL-3.0 License**: Copyleft obligations. Modificaciones o deployments públicos deben ser open source. Revisar licencia antes de producción.

## 🐳 Instalación

### Paso 1: docker-compose.yml (simple con PostgreSQL)

```yaml
version: '3.8'
services:
  vikunja:
    image: vikunja/vikunja:latest
    container_name: vikunja
    restart: unless-stopped
    ports:
      - "3456:3456"
    volumes:
      - ./files:/app/vikunja/files
    environment:
      - VIKUNJA_DATABASE_TYPE=postgres
      - VIKUNJA_DATABASE_HOST=db
      - VIKUNJA_DATABASE_PORT=5432
      - VIKUNJA_DATABASE_USER=vikunja
      - VIKUNJA_DATABASE_PASSWORD=securepass123
      - VIKUNJA_DATABASE_DATABASE=vikunja
    depends_on:
      - db

  db:
    image: postgres:15
    container_name: vikunja-db
    restart: unless-stopped
    environment:
      - POSTGRES_DB=vikunja
      - POSTGRES_USER=vikunja
      - POSTGRES_PASSWORD=securepass123
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

### Paso 2: Iniciar Vikunja

```bash
docker compose up -d
# Espera ~20 segundos para que inicie (migration DB)
docker compose logs -f
```

### Paso 3: Acceder a Vikunja

```bash
# Abre en navegador:
http://localhost:3456

# Desde otro dispositivo:
http://192.168.1.100:3456
```

✅ **Vikunja Web UI**: http://localhost:3456  
📋 **API REST**: http://localhost:3456/api/v1  
📅 **CalDAV URL**: http://localhost:3456/dav

## ⚙️ Configuración

1. **Base de datos** - Configurar `VIKUNJA_DATABASE_TYPE` (postgres, mysql, sqlite)
2. **Credenciales DB** - Ajustar `VIKUNJA_DATABASE_USER`, `VIKUNJA_DATABASE_PASSWORD`, `VIKUNJA_DATABASE_DATABASE`
3. **Puerto** - Cambiar `"3456:3456"` si hay conflictos
4. **Volúmenes** - Mapear `./files:/app/vikunja/files` para persistencia de adjuntos
5. **Variables de entorno** - Consultar [docs oficiales](https://vikunja.io/docs/config-options/) para opciones avanzadas (JWT, CORS, email, etc.)

## 🚀 Primeros pasos

1. Abre `http://localhost:3456` en navegador
2. Click **"Register"** para crear usuario admin
3. Ingresa email y contraseña
4. Login con credenciales
5. Dashboard aparece con proyectos vacíos
6. Create nuevo proyecto (namespace → project)
7. Agrega tareas en kanban board
8. Invita colaboradores si es equipo

💡 **First user = admin**: El primer usuario registrado obtiene permisos admin.

## 💡 Casos de uso

- **Team project management**: Equipos pequeños-medianos, task tracking, multiple projects
- **CalDAV sync**: Sincroniza con Thunderbird, Outlook, unified task calendar
- **Agile/Kanban workflow**: Kanban boards, sprint planning, Gantt charts
- **Personal + team**: Proyectos personales, proyectos compartidos, namespaces
- **API integrations**: REST API, Webhooks, custom tools, n8n automation
- **Enterprise self-hosted**: Full data control, custom deployment, no vendor lock-in
- **Privacy-focused**: Self-hosted, AGPL guarantees, community-driven

## 🔒 Acceso remoto seguro

### Habilitar HTTPS con Caddy

```yaml
# Agregar service caddy al docker-compose.yml
caddy:
  image: caddy:latest
  ports:
    - "80:80"
    - "443:443"
  volumes:
    - ./Caddyfile:/etc/caddy/Caddyfile
    - caddy_data:/data
```

```caddyfile
# Caddyfile
vikunja.tudominio.com {
    reverse_proxy vikunja:3456
}
```

## 🛠️ Gestión y mantenimiento

```bash
# Ver estado
docker compose ps

# Ver logs
docker compose logs -f vikunja
# Ver también DB logs
docker compose logs -f db

# Detener Vikunja
docker compose down

# Actualizar versión
docker compose pull
docker compose up -d
# DB migration ocurre automáticamente

# Backup de datos
docker compose exec db pg_dump -U vikunja vikunja > backup.sql
# Backup carpeta files
tar -czf vikunja_files_backup.tar.gz files/

# Monitorear consumo
docker stats vikunja db
# Típicamente:
# Vikunja CPU: 2-10% (según uso)
# Vikunja RAM: 100-500MB
# PostgreSQL RAM: 200-1GB
```

## 📝 Licencia

**AGPL-3.0** (copyleft) - Ver [LICENSE](https://github.com/vikunja/vikunja/blob/main/LICENSE) en el repositorio oficial.

---

> 📖 **Guía completa**: [Cómo instalar Vikunja en Docker - Task Manager autohospedado](https://genbyte.blogspot.com/2026/09/como-instalar-vikunja-en-docker-task.html)  
> 🎥 **Video tutorial**: [Canal GENBYTE en YouTube](https://www.youtube.com/@genbyte)