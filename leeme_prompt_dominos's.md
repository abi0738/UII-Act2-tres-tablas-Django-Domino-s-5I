# Proyecto: UIII_dominos_0738 (Django)

**Lenguaje:** Python

**Framework:** Django

**Editor:** VS Code

---

## 0. Estructura propuesta de carpetas (al inicio)

```
UIII_dominos_0738/
├─ .venv/
├─ backend_dominos/
│  ├─ backend_dominos/
│  │  ├─ __init__.py
│  │  ├─ settings.py
│  │  ├─ urls.py
│  │  └─ wsgi.py
│  ├─ manage.py
├─ app_dominos/
│  ├─ migrations/
│  ├─ templates/
│  │  ├─ base.html
│  │  ├─ header.html
│  │  ├─ navbar.html
│  │  ├─ footer.html
│  │  ├─ inicio.html
│  │  └─ clientes/
│  │     ├─ agregar_cliente.html
│  │     ├─ ver_clientes.html
│  │     ├─ actualizar_cliente.html
│  │     └─ borrar_cliente.html
│  ├─ __init__.py
│  ├─ admin.py
│  ├─ apps.py
│  ├─ models.py
│  ├─ views.py
│  ├─ urls.py
│  └─ tests.py
```

---

## 1–11: Procedimientos paso a paso (comandos) **para Windows** (ajusta si usas Linux/Mac)

1. **Crear carpeta del Proyecto** `UIII_dominos_0738`:

```bash
# en la ubicación que quieras (ej. Desktop)
mkdir UIII_dominos_0738
cd UIII_dominos_0738
```

2. **Abrir VS Code en la carpeta** `UIII_dominos_0738`:

```bash
# desde la terminal (si code está en PATH)
code .
```

3. **Abrir terminal en VS Code**: Menú `Terminal` → `New Terminal` (o `Ctrl+`` ).

4. **Crear entorno virtual `.venv` desde terminal**:

```bash
# Windows (PowerShell)
python -m venv .venv
# o si usas cmd
py -m venv .venv
```

5. **Activar el entorno virtual**:

```powershell
# PowerShell
.\.venv\Scripts\Activate.ps1
# Si da error de ejecución en PowerShell, en cmd usar:
.\.venv\Scripts\activate
```

6. **Activar intérprete de Python en VS Code**:

* En VS Code abrir la paleta (`Ctrl+Shift+P`) → `Python: Select Interpreter` → elegir la ruta `.../UIII_dominos_0738/.venv/...`.

7. **Instalar Django**:

```bash
pip install django
# verificar
python -m django --version
```

8. **Crear proyecto `backend_dominos` sin duplicar carpeta**:

```bash
# En la carpeta UIII_dominos_0738
django-admin startproject backend_dominos .
# el punto evita crear una carpeta extra y coloca manage.py en la raíz del proyecto
```

9. **Ejecutar servidor en el puerto 8038**:

```bash
python manage.py runserver 8038
```

10. **Copiar y pegar el link en el navegador**:

* Cuando el servidor arranca verás: `Starting development server at http://127.0.0.1:8038/` — copia esa URL y pégala en tu navegador.

11. **Crear aplicación `app_dominos`**:

```bash
python manage.py startapp app_dominos
```

---

## 12: Migraciones (makemigrations y migrate)

```bash
# después de crear/registrar modelos
python manage.py makemigrations
python manage.py migrate
```

---

## 13: Empezamos con el MODELO: CLIENTE (solo este por ahora)

**Archivo:** `app_dominos/models.py`

```python
from django.db import models

class Cliente(models.Model):
    nombre = models.CharField(max_length=100)
    correo = models.EmailField(unique=True)
    telefono = models.CharField(max_length=15)
    direccion = models.CharField(max_length=200)
    fecha_registro = models.DateField(auto_now_add=True)
    puntos = models.IntegerField(default=0)
    activo = models.BooleanField(default=True)

    def __str__(self):
        return self.nombre

# Dejar # MODELO: PRODUCTO y # MODELO: PEDIDO pendientes para después
```

---

## 14: Views de `app_dominos` (CRUD cliente)

**Archivo:** `app_dominos/views.py`

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Cliente

# Página inicio
def inicio_dominos(request):
    return render(request, 'inicio.html')

# Agregar cliente (muestra formulario)
def agregar_cliente(request):
    if request.method == 'POST':
        nombre = request.POST.get('nombre')
        correo = request.POST.get('correo')
        telefono = request.POST.get('telefono')
        direccion = request.POST.get('direccion')
        # crear cliente sin validaciones
        Cliente.objects.create(
            nombre=nombre,
            correo=correo,
            telefono=telefono,
            direccion=direccion
        )
        return redirect('ver_clientes')
    return render(request, 'clientes/agregar_cliente.html')

# Ver clientes
def ver_clientes(request):
    clientes = Cliente.objects.all()
    return render(request, 'clientes/ver_clientes.html', {'clientes': clientes})

# Actualizar cliente (muestra formulario con datos)
def actualizar_cliente(request, cliente_id):
    cliente = get_object_or_404(Cliente, pk=cliente_id)
    return render(request, 'clientes/actualizar_cliente.html', {'cliente': cliente})

# Realizar actualización (procesa POST)
def realizar_actualizacion_cliente(request, cliente_id):
    if request.method == 'POST':
        cliente = get_object_or_404(Cliente, pk=cliente_id)
        cliente.nombre = request.POST.get('nombre')
        cliente.correo = request.POST.get('correo')
        cliente.telefono = request.POST.get('telefono')
        cliente.direccion = request.POST.get('direccion')
        cliente.save()
        return redirect('ver_clientes')
    return redirect('ver_clientes')

# Borrar cliente (confirmación y borrado)
def borrar_cliente(request, cliente_id):
    cliente = get_object_or_404(Cliente, pk=cliente_id)
    if request.method == 'POST':
        cliente.delete()
        return redirect('ver_clientes')
    return render(request, 'clientes/borrar_cliente.html', {'cliente': cliente})
```

---

## 15: Crear carpeta `templates` dentro de `app_dominos`

* Ya incluido en la estructura. En settings.py se usa la búsqueda de templates por app si `APP_DIRS=True`.

---

## 16–20: Archivos HTML (resumen)

**Nota:** No se usa `forms.py`. Los formularios serán HTML simples que envían `POST`.

### `templates/base.html`

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Domino's - Sistema</title>
  <!-- Bootstrap CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
  {% include 'header.html' %}
  {% include 'navbar.html' %}

  <main class="container my-4">
    {% block content %}{% endblock %}
  </main>

  {% include 'footer.html' %}

  <!-- Bootstrap JS -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

### `templates/header.html`

```html
<header class="container py-3">
  <h1 class="h3">Sistema de Administración Domino’s</h1>
</header>
```

### `templates/navbar.html`

```html
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">🍕 Domino’s</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navMenu">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navMenu">
      <ul class="navbar-nav me-auto mb-2 mb-lg-0">
        <li class="nav-item"><a class="nav-link" href="/">Inicio</a></li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">Clientes</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="/clientes/agregar/">Agregar Cliente</a></li>
            <li><a class="dropdown-item" href="/clientes/">Ver clientes</a></li>
          </ul>
        </li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">Productos</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar producto</a></li>
            <li><a class="dropdown-item" href="#">Ver productos</a></li>
          </ul>
        </li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">Pedidos</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar pedido</a></li>
            <li><a class="dropdown-item" href="#">Ver pedidos</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

> **Nota:** Los iconos principales están en la etiqueta del `brand` (emoji). No se agregaron íconos en submenús por requerimiento.

### `templates/footer.html`

```html
<footer class="bg-light text-center py-3 mt-auto" style="position: fixed; bottom: 0; left: 0; right:0;">
  <div class="container">
    <small>&copy; {{ now|date:'Y' }} - Ashley Vega, CBTis 128. Todos los derechos reservados.</small>
  </div>
</footer>
```

### `templates/inicio.html`

```html
{% extends 'base.html' %}
{% block content %}
  <div class="text-center">
    <h2>Bienvenido al Sistema de Administración Domino’s</h2>
    <p>Gestiona clientes, productos y pedidos desde aquí.</p>
    <img src="https://upload.wikimedia.org/wikipedia/commons/7/79/Dominos_pizza_logo.svg" class="img-fluid" alt="Domino's" style="max-width:300px;">
  </div>
{% endblock %}
```

---

## 21–22: Plantillas para clientes (`app_dominos/templates/clientes/`)

### `agregar_cliente.html`

```html
{% extends 'base.html' %}
{% block content %}
<h3>Agregar Cliente</h3>
<form method="post">{% csrf_token %}
  <div class="mb-3"><label>Nombre</label><input name="nombre" class="form-control"></div>
  <div class="mb-3"><label>Correo</label><input name="correo" class="form-control" type="email"></div>
  <div class="mb-3"><label>Teléfono</label><input name="telefono" class="form-control"></div>
  <div class="mb-3"><label>Dirección</label><input name="direccion" class="form-control"></div>
  <button class="btn btn-primary" type="submit">Agregar</button>
</form>
{% endblock %}
```

### `ver_clientes.html` (tabla con botones ver, editar, borrar)

```html
{% extends 'base.html' %}
{% block content %}
<h3>Clientes</h3>
<a href="/clientes/agregar/" class="btn btn-success mb-3">Agregar Cliente</a>
<table class="table table-striped">
  <thead><tr><th>ID</th><th>Nombre</th><th>Correo</th><th>Tel</th><th>Acciones</th></tr></thead>
  <tbody>
    {% for cliente in clientes %}
    <tr>
      <td>{{ cliente.id }}</td>
      <td>{{ cliente.nombre }}</td>
      <td>{{ cliente.correo }}</td>
      <td>{{ cliente.telefono }}</td>
      <td>
        <a class="btn btn-info btn-sm" href="/clientes/{{ cliente.id }}/">Ver</a>
        <a class="btn btn-warning btn-sm" href="/clientes/{{ cliente.id }}/editar/">Editar</a>
        <a class="btn btn-danger btn-sm" href="/clientes/{{ cliente.id }}/borrar/">Borrar</a>
      </td>
    </tr>
    {% empty %}
    <tr><td colspan="5">No hay clientes</td></tr>
    {% endfor %}
  </tbody>
</table>
{% endblock %}
```

### `actualizar_cliente.html`

```html
{% extends 'base.html' %}
{% block content %}
<h3>Actualizar Cliente</h3>
<form method="post" action="/clientes/{{ cliente.id }}/actualizar/">{% csrf_token %}
  <div class="mb-3"><label>Nombre</label><input name="nombre" value="{{ cliente.nombre }}" class="form-control"></div>
  <div class="mb-3"><label>Correo</label><input name="correo" value="{{ cliente.correo }}" class="form-control" type="email"></div>
  <div class="mb-3"><label>Teléfono</label><input name="telefono" value="{{ cliente.telefono }}" class="form-control"></div>
  <div class="mb-3"><label>Dirección</label><input name="direccion" value="{{ cliente.direccion }}" class="form-control"></div>
  <button class="btn btn-primary" type="submit">Guardar cambios</button>
</form>
{% endblock %}
```

### `borrar_cliente.html`

```html
{% extends 'base.html' %}
{% block content %}
<h3>Confirmar borrado</h3>
<p>¿Desea eliminar al cliente <strong>{{ cliente.nombre }}</strong>?</p>
<form method="post">{% csrf_token %}
  <a href="/clientes/" class="btn btn-secondary">Cancelar</a>
  <button class="btn btn-danger" type="submit">Borrar</button>
</form>
{% endblock %}
```

---

## 23: **No utilizar forms.py** — ya respetado, usamos `request.POST`.

---

## 24: `app_dominos/urls.py` (enrutar funciones de views.py)

**Archivo:** `app_dominos/urls.py`

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_dominos, name='inicio'),
    path('clientes/', views.ver_clientes, name='ver_clientes'),
    path('clientes/agregar/', views.agregar_cliente, name='agregar_cliente'),
    path('clientes/<int:cliente_id>/', views.actualizar_cliente, name='ver_cliente'),
    path('clientes/<int:cliente_id>/editar/', views.actualizar_cliente, name='editar_cliente'),
    path('clientes/<int:cliente_id>/actualizar/', views.realizar_actualizacion_cliente, name='realizar_actualizacion_cliente'),
    path('clientes/<int:cliente_id>/borrar/', views.borrar_cliente, name='borrar_cliente'),
]
```

> **Nota:** `ver` y `editar` usan la misma vista para mostrar el formulario; `realizar_actualizacion_cliente` procesa el POST.

---

## 25: Agregar `app_dominos` en `backend_dominos/settings.py`

En `INSTALLED_APPS` agregar:

```python
INSTALLED_APPS = [
    # apps por defecto...
    'app_dominos',
]
```

Asegurarse de que en `TEMPLATES` `"APP_DIRS": True` esté activado (valor por defecto). También agregar `TIME_ZONE` si quieres.

---

## 26: Configurar `backend_dominos/urls.py` para enlazar con `app_dominos`

**Archivo:** `backend_dominos/urls.py`

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_dominos.urls')),
]
```

---

## 27: Registrar modelo `Cliente` en `app_dominos/admin.py` y volver a migrar

**Archivo:** `app_dominos/admin.py`

```python
from django.contrib import admin
from .models import Cliente

@admin.register(Cliente)
class ClienteAdmin(admin.ModelAdmin):
    list_display = ('id', 'nombre', 'correo', 'telefono', 'activo', 'fecha_registro')
    search_fields = ('nombre', 'correo')
```

Luego:

```bash
python manage.py makemigrations app_dominos
python manage.py migrate
```

---

## 28: Estética y validaciones

* Usamos Bootstrap para mantener colores suaves y modernos.
* **No validamos entradas** (tal como solicitaste).

---

## 29: Crear la estructura completa de carpetas y archivos

* Siguiendo la sección de **Estructura propuesta** crea las carpetas y archivos con los contenidos mostrados.

---

## 30: Proyecto totalmente funcional (cliente CRUD)

* Al seguir los pasos tendrás: agregar/ver/editar/borrar clientes y acceso a `admin`.

---

## 31: Ejecutar servidor en el puerto 8038 (repetición de paso 9)

```bash
python manage.py runserver 8038
```

---

### Consejos rápidos y resoluciones de problemas:

* Si `python` o `py` no funciona, verifica la instalación de Python y que esté en PATH.
* Si PowerShell bloquea la activación, ejecuta: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser` como administrador o usa `activate` en cmd.
* Si la plantilla no carga, asegúrate de reiniciar el servidor tras crear archivos.

---

Si quieres que te entregue **archivo por archivo** (copiar/pegar en el chat) o generar un `.zip` descargable, dímelo y lo preparo. También puedo agregar los modelos de `Producto` y `Pedido` cuando quieras.
