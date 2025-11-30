# --SISTEMA DE AUTENTICACIÓN CON DJANGO ALLAUTH--
Este repositorio contiene el proyecto individual de un sistema completo de completo de autenticación de usuarios implementado con Django, django-allauth, Docker y PostgreSQL. Incluye registro, login, recuperación de contraseña y verificación de email con un diseño elegante usando Tailwind CSS. 
[![Django](https://img.shields.io/badge/Django-5.0-green.svg)](https://www.djangoproject.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-blue.svg)](https://www.postgresql.org/)
[![Docker](https://img.shields.io/badge/Docker-Ready-blue.svg)](https://www.docker.com/)

## Características del proyecto
 
- ✅ **Registro de usuarios** 
- ✅ **Inicio y cierre de sesión** 
- ✅ **Recuperación de contraseña** 
- ✅ **Cambio de contraseña** 
- ✅ **Verificación de email** 
- ✅ **Contenedorización completa** 
- ✅ **Base de datos PostgreSQL** 

---

## Requisitos Previos

Se debe tener instalado:
- **Docker Desktop** ([Descargar aquí](https://www.docker.com/products/docker-desktop))
  - Windows 10/11 Pro, Enterprise o Education
  - macOS 10.15 o superior
  - Linux con kernel 3.10+
- **Git** (opcional, para clonar el repositorio aunque más recomendable)

> **Nota:** Docker instalará lo necesario dentro del contenedor


## Instalación

### Opción 1: Clonar desde Git usando git Bash

```bash
# Clonar el repositorio
git clone https://github.com/tu-usuario/proyecto-django-allauth.git

# Entrar al directorio
cd proyecto-django-allauth
```

### Opción 2: Descargar ZIP

1. Descarga el proyecto como ZIP
2. Extraer el contenido
3. Abre la terminal en la carpeta del proyecto

---

## Ejecución con Docker

### Paso 1: Una vez dentro de la carpeta, construir y levantar los Contenedores
### NOTA IMPORTANTE: Es importante que se tenga en ejecución Docker Desktop
Ejecuta el siguiente comando: 
```bash
docker-compose up --build
```

Con solo este comando, Docker hará lo siguiente:
- Construir la imagen de Docker para Django
- Descargar PostgreSQL 15 Alpine y las demás herramientas requeridas
- Crear el volumen para la base de datos
- Ejecutar las migraciones automáticamente (ya que esta configurado en el archivo de `docker-compose.yml`)
- Inicia el servidor de desarrollo en el puerto 8000

> **IMPORTANTE:** Las migraciones se ejecutan automáticamente gracias a la configuración en `docker-compose.yml`:
> ```yaml
> command: >
>   sh -c "
>   python manage.py migrate --noinput &&
>   python manage.py runserver 0.0.0.0:8000
>   "
> ```
> Por lo tanto, **Ya NO se necesita ejecutar** `python manage.py migrate` manualmente PERO en caso de que cambies alugun model..
> SE usa el comando manual de: 
```bash
docker-compose exec web python manage.py makemigrations
```
### Paso 2: Al levantarlo, en la terminal, revisa que todo este en orden.

En la terminal verás algo como:

```
django_postgres_db  | database system is ready to accept connections
django_web_app      | Applying contenttypes.0001_initial... OK
django_web_app      | Applying auth.0001_initial... OK
django_web_app      | Applying account.0001_initial... OK
django_web_app      | ...
django_web_app      | System check identified no issues (0 silenced).
django_web_app      | November 24, 2025 - 12:00:00
django_web_app      | Django version 5.0, using settings 'myproject.settings'
django_web_app      | Starting development server at http://0.0.0.0:8000/
django_web_app      | Quit the server with CONTROL-C.
```

✅ Un mensaje en terminal así, indica que todo está en orden.

### Ejecutar en Segundo Plano (Opcional)

Si se prefiere que los contenedores corran en segundo plano podemos usar el comando de :

```bash
docker-compose up -d
```

Para ver los logs, usamos el sigueinte comando:

```bash
docker-compose logs -f
```

---

##  Crear Superusuario

Para acceder al panel de administración de Django, se necesita crear un superusuario para ello hay dos metodos a seguir.

### Método 1: Con contenedores corriendo

**Se abre una NUEVA terminal** (dejando la primera corriendo) y ejecutar:

```bash
docker-compose exec -it web python manage.py createsuperuser
```

Te pedirá:

```
Email address: 
Password: 
Password (again): 
Superuser created successfully.
```

### Método 2: Usando script automático (opcional)

Si prefieres crear el superusuario automáticamente, puedes usar el script incluido:

```bash
docker-compose exec web python setup_admin.py
```

Esto creará un superusuario con:
- **Email:** `admin@admin.com`
- **Password:** `admin123`

> **Importante:** Es recomendable que ya en producción estas credenciales se cambien.

---

## Revisión de la aplicación 

Una vez que los contenedores estén corriendo:

### Aplicación Principal
```
http://localhost:8000/
```

### Panel de Administración
```
http://localhost:8000/admin/
```
Para acceder a él, usarás: 
- Usuario: El email que configuraste en el superusuario
- Contraseña: La contraseña que configuraste

### URLs Disponibles

| URL | Descripción |
|-----|-------------|
| `/` | Página de inicio |
| `/accounts/signup/` | Registro de usuarios |
| `/accounts/login/` | Inicio de sesión |
| `/accounts/logout/` | Cierre de sesión |
| `/accounts/password/change/` | Cambiar contraseña |
| `/accounts/password/reset/` | Recuperar contraseña |
| `/admin/` | Panel de administración |

---

## Funcionalidades

### 1. Registro de Usuarios

- Formulario de registro con validación
- Verificación de email obligatoria
- Validaciones de contraseña:
  - Mínimo 8 caracteres
  - No completamente numérica
  - No similar a información personal

### 2. Inicio de Sesión

- Login con email (no requiere username)
- Protección contra fuerza bruta
- Redirección automática después del login

### 3. Recuperación de Contraseña

- Solicitud mediante email
- Enlace único con token seguro
- Expiración de 24 horas
- En **desarrollo**: Los emails se muestran en la consola

**Ver emails en desarrollo:**
```bash
docker-compose logs -f web
```

Busca el enlace como:
```
http://localhost:8000/accounts/password/reset/key/xxxxx-xxxxx/
```

### 4. Verificación de Email

- Email de verificación después del registro
- Enlace único por usuario
- Reenvío de email disponible

---

##  Estructura del Proyecto

```
proyecto-django-allauth/
├── 📂 myproject/              # Configuración principal de Django
│   ├── settings.py            # Configuraciones del proyecto
│   ├── urls.py                # Rutas principales
│   ├── wsgi.py                # WSGI configuration
│   └── asgi.py                # ASGI configuration
├── 📂 accounts/               # Aplicación de usuarios
│   ├── models.py
│   ├── views.py
│   ├── admin.py
│   └── apps.py
├── 📂 templates/              # Plantillas HTML
│   ├── home.html              # Página principal
│   └── 📂 account/            # Templates de django-allauth
│       ├── login.html
│       ├── signup.html
│       ├── logout.html
│       ├── password_change.html
│       ├── password_reset.html
│       ├── password_reset_done.html
│       ├── password_reset_from_key.html
│       ├── password_reset_from_key_done.html
│       ├── email_confirm.html
│       └── verification_sent.html
├── 🐳 docker-compose.yml      # Orquestación de contenedores
├── 🐳 Dockerfile              # Imagen de Docker para Django
├── 📄 requirements.txt        # Dependencias de Python
├── 📄 manage.py               # CLI de Django
├── 📄 README.md               # Este archivo
└── 📄 .gitignore              # Archivos ignorados por Git
```

## COMANDOS 
### Django

```bash
# Crear migraciones (si modificas models)
docker-compose exec web python manage.py makemigrations

# Aplicar migraciones
docker-compose exec web python manage.py migrate

# Crear superusuario
docker-compose exec -it web python manage.py createsuperuser

# Abrir shell de Django
docker-compose exec web python manage.py shell

# Recopilar archivos estáticos
docker-compose exec web python manage.py collectstatic
```

### Gestión de Base de Datos

```bash
# Acceder a PostgreSQL
docker-compose exec db psql -U django_user -d django_db

# Hacer backup de la base de datos
docker-compose exec db pg_dump -U django_user django_db > backup.sql

# Restaurar backup
docker-compose exec -T db psql -U django_user django_db < backup.sql

# Ver tablas en la base de datos
docker-compose exec db psql -U django_user -d django_db -c "\dt"
```

### Limpieza

```bash
# Detener y eliminar contenedores
docker-compose down

# Eliminar también volúmenes (BORRAR LA BASE DE DATOS)
docker-compose down -v

# Limpiar imágenes no usadas
docker system prune

# Reconstruir desde cero
docker-compose down -v
docker-compose build --no-cache
docker-compose up
```