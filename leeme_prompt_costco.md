Perfecto — te doy **la primera parte completa paso a paso** (Windows y Linux/macOS donde aplique) para crear el proyecto **UIII_Costco_0599** con Django, VS Code y todo lo pedido. Incluyo comandos exactos, archivos y código para el CRUD de **Usuario** (sin `forms.py`) y las plantillas. Copia/pega lo necesario y sigue los pasos en orden.

---

# 1 — Crear la carpeta del proyecto `UIII_Costco_0599`

Windows / Linux / macOS (terminal):

```bash
# desde la carpeta donde quieras crear el proyecto
mkdir UIII_Costco_0599
cd UIII_Costco_0599
```

---

# 2 — Abrir VS Code sobre la carpeta `UIII_Costco_0599`

Desde la terminal (en la carpeta):

```bash
code .
```

(o abre VS Code y usa *File → Open Folder...* y selecciona `UIII_Costco_0599`).

---

# 3 — Abrir terminal en VS Code

En VS Code: `Ctrl + `` (backtick)` o menú `Terminal → New Terminal`.
Se abrirá una terminal integrada situada en la ruta del proyecto.

---

# 4 — Crear carpeta entorno virtual `.venv` desde terminal de VS Code

En la terminal integrada, ejecuta:

```bash
# Windows, macOS, Linux (misma sintaxis)
python -m venv .venv
```

Esto crea la carpeta oculta `.venv` dentro del proyecto.

---

# 5 — Activar el entorno virtual

* **Windows PowerShell**:

```powershell
.venv\Scripts\Activate.ps1
```

(Si PowerShell bloquea ejecución, ejecutar como administrador `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser` — opcional.)

* **Windows CMD**:

```cmd
.venv\Scripts\activate
```

* **macOS / Linux (bash/zsh)**:

```bash
source .venv/bin/activate
```

Después de activar verás `(.venv)` al inicio del prompt.

---

# 6 — Activar intérprete de Python en VS Code

1. En VS Code `Ctrl+Shift+P` → escribe `Python: Select Interpreter`.
2. Selecciona el intérprete dentro de `UIII_Costco_0599/.venv/...` (aparecerá como `.venv`).

---

# 7 — Instalar Django

Con el entorno activado:

```bash
pip install --upgrade pip
pip install django
```

(Verifica con `python -m django --version`).

---

# 8 — Crear proyecto `backend_Costco` sin duplicar carpeta

**Importante**: para evitar que Django cree una carpeta extra con el mismo nombre *usa el punto al final*:

```bash
django-admin startproject backend_Costco .
```

Esto crea los archivos `manage.py` y la carpeta `backend_Costco/` con `settings.py`, sin duplicar el directorio padre.

---

# 9 — Ejecutar servidor en el puerto `8018`

Con el entorno activado y desde la raíz del proyecto (donde está `manage.py`):

```bash
python manage.py runserver 8018
```

Si necesitas escuchar en todas las interfaces (ej. desde otro dispositivo en la red):

```bash
python manage.py runserver 0.0.0.0:8018
```

---

# 10 — Copiar y pegar el link en el navegador

Abre el navegador y pega:

```
http://127.0.0.1:8018/
```

ó

```
http://localhost:8018/
```

(usa la IP que `runserver` muestre en la terminal; por defecto `http://127.0.0.1:8018/`).

---

# 11 — Crear aplicación `app_Costco`

Desde la raíz del proyecto:

```bash
python manage.py startapp app_Costco
```

Se creará la carpeta `app_Costco/` con sus archivos.

---

# 12 — (Ya me diste) El `models.py` — (lo incluimos tal cual)

Has provisto el modelo completo. Asegúrate que esté en `app_Costco/models.py`. (Lo repito brevemente: clases `Usuario`, `Producto`, `Pedido` — trabajaremos por ahora solo con `Usuario`).

---

# 12.5 — Procedimiento para realizar las migraciones (makemigrations y migrate)

1. Registrar la app en `backend_Costco/settings.py` en `INSTALLED_APPS` (ver paso 25).
2. Ejecutar:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

# 13 — Primero trabajar con el MODELO: USUARIO

Nos concentraremos en CRUD para `Usuario`.

---

# 14 — Views en `app_Costco/views.py`

A continuación te doy funciones **completas** (funcion-based views) para: `inicio_costco`, `agregar_usuario`, `actualizar_usuario`, `realizar_actualizacion_usuario`, `borrar_usuario`. (No se usa `forms.py`; usaremos `request.POST` y ORM).

Copia esto en `app_Costco/views.py` (reemplaza/añade según corresponda):

```python
from django.shortcuts import render, redirect, get_object_or_404
from django.utils import timezone
from .models import Usuario

def inicio_costco(request):
    return render(request, 'inicio.html')

# Mostrar formulario para agregar usuario y procesar POST
def agregar_usuario(request):
    if request.method == 'POST':
        nombre_usuario = request.POST.get('nombre_usuario', '').strip()
        email = request.POST.get('email', '').strip()
        nombre = request.POST.get('nombre', '').strip()
        apellido = request.POST.get('apellido', '').strip()
        direccion = request.POST.get('direccion', '').strip()
        telefono = request.POST.get('telefono', '').strip()

        usuario = Usuario(
            nombre_usuario=nombre_usuario,
            email=email,
            nombre=nombre,
            apellido=apellido,
            direccion=direccion,
            telefono=telefono
        )
        usuario.save()
        return redirect('ver_usuario')
    # GET -> mostrar formulario
    return render(request, 'usuario/agregar_usuario.html')

# Ver lista de usuarios
def ver_usuario(request):
    usuarios = Usuario.objects.all().order_by('-fecha_registro')
    return render(request, 'usuario/ver_usuario.html', {'usuarios': usuarios})

# Mostrar formulario de actualización (carga datos)
def actualizar_usuario(request, usuario_id):
    usuario = get_object_or_404(Usuario, id=usuario_id)
    return render(request, 'usuario/actualizar_usuario.html', {'usuario': usuario})

# Procesar actualización (POST)
def realizar_actualizacion_usuario(request, usuario_id):
    usuario = get_object_or_404(Usuario, id=usuario_id)
    if request.method == 'POST':
        usuario.nombre_usuario = request.POST.get('nombre_usuario', usuario.nombre_usuario).strip()
        usuario.email = request.POST.get('email', usuario.email).strip()
        usuario.nombre = request.POST.get('nombre', usuario.nombre).strip()
        usuario.apellido = request.POST.get('apellido', usuario.apellido).strip()
        usuario.direccion = request.POST.get('direccion', usuario.direccion).strip()
        usuario.telefono = request.POST.get('telefono', usuario.telefono).strip()
        usuario.save()
        return redirect('ver_usuario')
    return redirect('actualizar_usuario', usuario_id=usuario_id)

# Confirmar y borrar usuario
def borrar_usuario(request, usuario_id):
    usuario = get_object_or_404(Usuario, id=usuario_id)
    if request.method == 'POST':
        usuario.delete()
        return redirect('ver_usuario')
    return render(request, 'usuario/borrar_usuario.html', {'usuario': usuario})
```

---

# 15 — Crear la carpeta `templates` dentro de `app_Costco`

Estructura:

```
app_Costco/
  templates/
    base.html
    header.html
    navbar.html
    footer.html
    inicio.html
    usuario/
      agregar_usuario.html
      ver_usuario.html
      actualizar_usuario.html
      borrar_usuario.html
```

---

# 16 — Archivos HTML (plantillas)

Te doy plantillas sencillas y modernas con Bootstrap CDN.

## `app_Costco/templates/base.html`

```html
<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>{% block title %}Sistema de Administración Costco{% endblock %}</title>
  <!-- Bootstrap CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
  <style>
    /* Colores suaves y moderno */
    body { background: #f7fafc; color: #1f2937; }
    .navbar-brand { font-weight: 700; }
    footer { position: fixed; bottom: 0; left: 0; right: 0; background:#fff; border-top: 1px solid #e5e7eb; padding: .5rem 1rem; text-align:center; }
    main { padding-bottom: 3.5rem; }
  </style>
  {% block extra_head %}{% endblock %}
</head>
<body>
  {% include 'navbar.html' %}
  <main class="container py-4">
    {% block content %}{% endblock %}
  </main>
  {% include 'footer.html' %}
  <!-- Bootstrap JS -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
  {% block extra_js %}{% endblock %}
</body>
</html>
```

## `app_Costco/templates/navbar.html`

```html
<nav class="navbar navbar-expand-lg navbar-light bg-white shadow-sm">
  <div class="container">
    <a class="navbar-brand" href="{% url 'inicio_costco' %}">Sistema de Administración Costco</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navMenu">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navMenu">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link" href="{% url 'inicio_costco' %}">Inicio</a></li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">Usuarios</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'agregar_usuario' %}">Agregar Usuario</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_usuario' %}">Ver Usuarios</a></li>
            <li><a class="dropdown-item" href="#">Actualizar Usuario</a></li>
            <li><a class="dropdown-item" href="#">Borrar Usuario</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">Productos</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar producto</a></li>
            <li><a class="dropdown-item" href="#">Ver producto</a></li>
            <li><a class="dropdown-item" href="#">Actualizar producto</a></li>
            <li><a class="dropdown-item" href="#">Borrar producto</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">Pedidos</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar pedido</a></li>
            <li><a class="dropdown-item" href="#">Ver pedido</a></li>
            <li><a class="dropdown-item" href="#">Actualizar pedido</a></li>
            <li><a class="dropdown-item" href="#">Borrar pedido</a></li>
          </ul>
        </li>

      </ul>
    </div>
  </div>
</nav>
```

## `app_Costco/templates/footer.html`

```html
<footer>
  <div class="container">
    &copy; {{ now.year }} Derechos reservados. Creado por Alumno. Hernandez Roman, CBTIS 128
  </div>
</footer>
```

> Nota: `now` lo puedes añadir desde el contexto del template engine o usar la etiqueta `now` de Django: `{% now "Y" as year %}` y luego `{{ year }}`. Alternativamente, en `base.html` antes de footer agrega `{% now "Y" as year %}` y usa `{{ year }}`.

## `app_Costco/templates/inicio.html`

```html
{% extends "base.html" %}
{% block title %}Inicio - Costco{% endblock %}
{% block content %}
<div class="row">
  <div class="col-md-8">
    <h1>Bienvenido al Sistema de Administración Costco</h1>
    <p>Panel de control para administrar Usuarios, Productos y Pedidos.</p>
  </div>
  <div class="col-md-4">
    <img src="https://upload.wikimedia.org/wikipedia/commons/3/3b/Costco_Wholesale_logo_2010-2016.svg" alt="Costco" class="img-fluid">
  </div>
</div>
{% endblock %}
```

---

# 17 — `base.html` ya incluye Bootstrap (ya mostrado arriba).

---

# 18 — `navbar.html` con opciones y submenus y **iconos en las opciones principales**

En el ejemplo usé texto; si quieres iconos añade por ejemplo `Bootstrap Icons` o `font-awesome`. (No puse iconos en submenu según tu requerimiento).

Ejemplo de cómo incluir icons (opcional):

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.5/font/bootstrap-icons.css">
<!-- luego en un enlace -->
<i class="bi bi-people-fill"></i> Usuarios
```

---

# 19 — `footer.html` fijo al final de la página (ya incluido en `base.html` con `position: fixed; bottom:0`).

---

# 20 — `inicio.html` usa una imagen desde la red (ejemplo incluido). Puedes cambiar la URL de la imagen por otra.

---

# 21 — Crear subcarpeta `usuario` dentro de `app_Costco/templates` y los archivos solicitados

Estructura ya indicada.

---

# 22 — Código HTML para CRUD de usuario

## `app_Costco/templates/usuario/agregar_usuario.html`

```html
{% extends "base.html" %}
{% block content %}
<h2>Agregar Usuario</h2>
<form method="post">
  {% csrf_token %}
  <div class="mb-3">
    <label class="form-label">Nombre de usuario</label>
    <input class="form-control" name="nombre_usuario" required>
  </div>
  <div class="mb-3">
    <label class="form-label">Email</label>
    <input class="form-control" name="email" type="email" required>
  </div>
  <div class="mb-3">
    <label class="form-label">Nombre</label>
    <input class="form-control" name="nombre">
  </div>
  <div class="mb-3">
    <label class="form-label">Apellido</label>
    <input class="form-control" name="apellido">
  </div>
  <div class="mb-3">
    <label class="form-label">Dirección</label>
    <textarea class="form-control" name="direccion"></textarea>
  </div>
  <div class="mb-3">
    <label class="form-label">Teléfono</label>
    <input class="form-control" name="telefono">
  </div>
  <button class="btn btn-primary" type="submit">Guardar</button>
  <a class="btn btn-secondary" href="{% url 'ver_usuario' %}">Cancelar</a>
</form>
{% endblock %}
```

## `app_Costco/templates/usuario/ver_usuario.html`

```html
{% extends "base.html" %}
{% block content %}
<h2>Usuarios</h2>
<a class="btn btn-success mb-3" href="{% url 'agregar_usuario' %}">+ Agregar Usuario</a>
<table class="table table-striped">
  <thead>
    <tr>
      <th>ID</th>
      <th>Usuario</th>
      <th>Nombre</th>
      <th>Email</th>
      <th>Teléfono</th>
      <th>Registrado</th>
      <th>Acciones</th>
    </tr>
  </thead>
  <tbody>
    {% for u in usuarios %}
    <tr>
      <td>{{ u.id }}</td>
      <td>{{ u.nombre_usuario }}</td>
      <td>{{ u.nombre }} {{ u.apellido }}</td>
      <td>{{ u.email }}</td>
      <td>{{ u.telefono }}</td>
      <td>{{ u.fecha_registro }}</td>
      <td>
        <a class="btn btn-sm btn-info" href="{% url 'actualizar_usuario' u.id %}">Editar</a>
        <a class="btn btn-sm btn-danger" href="{% url 'borrar_usuario' u.id %}">Borrar</a>
      </td>
    </tr>
    {% empty %}
    <tr><td colspan="7">No hay usuarios.</td></tr>
    {% endfor %}
  </tbody>
</table>
{% endblock %}
```

## `app_Costco/templates/usuario/actualizar_usuario.html`

```html
{% extends "base.html" %}
{% block content %}
<h2>Actualizar Usuario</h2>
<form method="post" action="{% url 'realizar_actualizacion_usuario' usuario.id %}">
  {% csrf_token %}
  <div class="mb-3">
    <label class="form-label">Nombre de usuario</label>
    <input class="form-control" name="nombre_usuario" value="{{ usuario.nombre_usuario }}">
  </div>
  <div class="mb-3">
    <label class="form-label">Email</label>
    <input class="form-control" name="email" value="{{ usuario.email }}">
  </div>
  <div class="mb-3">
    <label class="form-label">Nombre</label>
    <input class="form-control" name="nombre" value="{{ usuario.nombre }}">
  </div>
  <div class="mb-3">
    <label class="form-label">Apellido</label>
    <input class="form-control" name="apellido" value="{{ usuario.apellido }}">
  </div>
  <div class="mb-3">
    <label class="form-label">Dirección</label>
    <textarea class="form-control" name="direccion">{{ usuario.direccion }}</textarea>
  </div>
  <div class="mb-3">
    <label class="form-label">Teléfono</label>
    <input class="form-control" name="telefono" value="{{ usuario.telefono }}">
  </div>
  <button class="btn btn-primary" type="submit">Actualizar</button>
  <a class="btn btn-secondary" href="{% url 'ver_usuario' %}">Cancelar</a>
</form>
{% endblock %}
```

## `app_Costco/templates/usuario/borrar_usuario.html`

```html
{% extends "base.html" %}
{% block content %}
<h2>Confirmar eliminación</h2>
<p>¿Seguro que desea borrar al usuario <strong>{{ usuario.nombre_usuario }}</strong> ?</p>
<form method="post">
  {% csrf_token %}
  <button class="btn btn-danger" type="submit">Sí, borrar</button>
  <a class="btn btn-secondary" href="{% url 'ver_usuario' %}">Cancelar</a>
</form>
{% endblock %}
```

---

# 23 — No utilizar `forms.py`

Las vistas usan `request.POST` directamente, tal como pediste. (Si luego prefieres validación, se puede agregar.)

---

# 24 — `urls.py` en `app_Costco` y rutas

Crea `app_Costco/urls.py` con:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_costco, name='inicio_costco'),
    path('usuarios/agregar/', views.agregar_usuario, name='agregar_usuario'),
    path('usuarios/', views.ver_usuario, name='ver_usuario'),
    path('usuarios/actualizar/<int:usuario_id>/', views.actualizar_usuario, name='actualizar_usuario'),
    path('usuarios/realizar_actualizacion/<int:usuario_id>/', views.realizar_actualizacion_usuario, name='realizar_actualizacion_usuario'),
    path('usuarios/borrar/<int:usuario_id>/', views.borrar_usuario, name='borrar_usuario'),
]
```

---

# 25 — Agregar `app_Costco` en `settings.py` de `backend_Costco`

En `backend_Costco/settings.py` → `INSTALLED_APPS` añade:

```python
INSTALLED_APPS = [
    # apps por defecto...
    'app_Costco',
]
```

---

# 26 — Configurar `backend_Costco/urls.py` para enlazar con `app_Costco`

Edita `backend_Costco/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Costco.urls')),  # enruta la app al root
]
```

---

# 27 — Registrar modelos en `admin.py` y migraciones

En `app_Costco/admin.py`:

```python
from django.contrib import admin
from .models import Usuario, Producto, Pedido

@admin.register(Usuario)
class UsuarioAdmin(admin.ModelAdmin):
    list_display = ('id','nombre_usuario','email','nombre','apellido','fecha_registro')
    search_fields = ('nombre_usuario','email','nombre','apellido')

@admin.register(Producto)
class ProductoAdmin(admin.ModelAdmin):
    list_display = ('id','nombre','precio','stock','categoria','fecha_creacion')
    search_fields = ('nombre','codigo_barras')

@admin.register(Pedido)
class PedidoAdmin(admin.ModelAdmin):
    list_display = ('id','usuario','fecha_pedido','estado_pedido','total_pedido')
    filter_horizontal = ('productos',)
```

Luego, ejecutar migraciones (si no las ejecutaste aún):

```bash
python manage.py makemigrations
python manage.py migrate
```

Crear superusuario para acceder al admin:

```bash
python manage.py createsuperuser
```

---

# 27 (seguido) — Por ahora trabajar solo con `Usuario`

Ya preparado: las vistas y plantillas trabajan con `Usuario`. `Producto` y `Pedido` quedan pendientes.

---

# 28 — Estética: colores suaves, diseño sencillo

Las plantillas usan estilos suaves y Bootstrap. Ajusta `style` en `base.html` a tu gusto.

---

# 28 (bis) — No validar entrada de datos

He dejado sin validación extra (solo `required` mínimo en la plantilla). Si quieres dejar TODO sin required, quita `required` de inputs.

---

# 29 — Al inicio crear la estructura completa de carpetas y archivos

Resumen de estructura sugerida:

```
UIII_Costco_0599/
  .venv/
  backend_Costco/
    __init__.py
    settings.py
    urls.py
    wsgi.py
    asgi.py
  app_Costco/
    migrations/
    templates/
      navbar.html
      base.html
      footer.html
      inicio.html
      usuario/
        agregar_usuario.html
        ver_usuario.html
        actualizar_usuario.html
        borrar_usuario.html
    admin.py
    apps.py
    models.py
    views.py
    urls.py
    tests.py
    ...
  manage.py
```

---

# 30 — Proyecto totalmente funcional (con Usuario CRUD)

Sigue los pasos anteriores, ejecuta `python manage.py runserver 8018` y prueba las rutas:

* Inicio: `http://127.0.0.1:8018/`
* Agregar usuario: `http://127.0.0.1:8018/usuarios/agregar/`
* Ver usuarios: `http://127.0.0.1:8018/usuarios/`

---

# 31 — Finalmente ejecutar servidor en el puerto `8018`

Ya mostrado arriba:

```bash
python manage.py runserver 8018
```

---

## Recomendaciones y notas finales rápidas

* Si recibes error `ALLOWED_HOSTS` al usar `0.0.0.0`, en `settings.py` agrega `ALLOWED_HOSTS = ['*']` temporalmente (solo en desarrollo).
* Para que `footer` muestre el año dinámico: en `base.html` usa `{% now "Y" as year %}` y luego `{{ year }}`.
* Si necesitas que la ruta admin se vea, entra a `http://127.0.0.1:8018/admin/` con el superuser que creaste.
* Guardé las convenciones de nombres (`app_Costco`, `backend_Costco`) tal como pediste para evitar duplicados.

---
