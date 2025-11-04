# Proyecto: UIII_Crunchyroll_0659 (Primera parte)

**Lenguaje:** Python

**Framework:** Django

**Editor:** VS Code

---

## Resumen

Este documento contiene: 1) procedimiento paso a paso para crear el proyecto Django, 2) estructura completa de carpetas y archivos, 3) comandos concretos para terminal, 4) código de `models.py` (tal como proporcionado), 5) vistas (`views.py`) para CRUD de Suscripción, 6) `urls.py` de la app, 7) registros en `admin.py`, 8) instrucciones para templates (base, navbar, header, footer, inicio) y las plantillas de Suscripciones, 9) migraciones y ejecución en puerto `8032`.

> **Nota importante:** por indicación, **solo trabajaremos por ahora con el MODELO: Suscripcion**. Los modelos `Usuario` y `Contenido` quedan pendientes.

---

## 1. Crear carpeta del proyecto

**Nombre de la carpeta principal:** `UIII_Crunchyroll_0659`

En el Explorador de archivos (o terminal):

```powershell
mkdir UIII_Crunchyroll_0659
cd UIII_Crunchyroll_0659
```

---

## 2. Abrir la carpeta en VS Code

En VS Code: `File > Open Folder...` y seleccionar `UIII_Crunchyroll_0659`.

O desde terminal (si `code` está en PATH):

```powershell
code .
```

---

## 3. Abrir terminal en VS Code

Desde VS Code: `Terminal > New Terminal`.

(Se abrirá por defecto en la ruta del proyecto `UIII_Crunchyroll_0659`.)

---

## 4. Crear carpeta entorno virtual `.venv` desde terminal (Windows)

```powershell
python -m venv .venv
```

Esto crea la carpeta oculta (o no) `.venv` dentro de `UIII_Crunchyroll_0659`.

---

## 5. Activar el entorno virtual

**Windows (PowerShell):**

```powershell
.\.venv\Scripts\Activate.ps1
```

**Windows (cmd):**

```cmd
.\.venv\Scripts\activate
```

**Linux / macOS:**

```bash
source .venv/bin/activate
```

(Tras activarlo verás `(.venv)` en el prompt.)

---

## 6. Seleccionar intérprete de Python en VS Code

En VS Code: `Ctrl+Shift+P` → `Python: Select Interpreter` → elegir `.venv` (aparecerá en la lista como `.venv\Scripts\python.exe`).

---

## 7. Instalar Django

Con el entorno activado:

```bash
pip install django
```

(Se recomienda `pip install django==4.2` si quiere una versión específica, pero no es obligatorio.)

---

## 8. Crear proyecto `backend_Crunchyroll` sin duplicar carpeta

Dentro de `UIII_Crunchyroll_0659` ejecutar:

```bash
django-admin startproject backend_Crunchyroll .
```

**Importante:** el `.` final evita que se cree una carpeta extra `backend_Crunchyroll/backend_Crunchyroll`.

---

## 9. Ejecutar servidor en el puerto 8032 (temporalmente antes de crear app)

```bash
python manage.py runserver 8032
```

---

## 10. Copiar y pegar el link en el navegador

El servidor mostrará una url similar a `http://127.0.0.1:8032/`. Copiarla y abrir en el navegador.

---

## 11. Crear aplicación `app_Crunchyroll`

Con el entorno activado y en la raíz del proyecto:

```bash
python manage.py startapp app_Crunchyroll
```

---

## 12. `models.py` (usar exactamente el contenido proporcionado)

Colocar este código dentro de `app_Crunchyroll/models.py` **sin modificar nada** (tal como fue entregado):

```python
from django.db import models
from django.core.validators import MinValueValidator, MaxValueValidator

# ==========================================
# MODELO: SUSCRIPCIÓN
# ==========================================
class Suscripcion(models.Model):
    nombre_plan = models.CharField(max_length=50, unique=True)
    precio = models.DecimalField(max_digits=5, decimal_places=2)
    calidad_video = models.CharField(max_length=20)
    num_dispositivos = models.IntegerField()
    beneficio_extra = models.TextField()
    descarga_offline = models.BooleanField(default=False)
    
    # Relación muchos a muchos: una suscripción puede tener varios contenidos exclusivos
    contenidos_exclusivos = models.ManyToManyField(
        'Contenido', 
        related_name='suscripciones_exclusivas',
        blank=True
    )
    # Relación muchos a muchos: una suscripción puede tener varios usuarios premium
    usuarios_premium = models.ManyToManyField(
        'Usuario', 
        related_name='suscripciones_adicionales',
        blank=True
    )

    class Meta:
        db_table = 'suscripcion'
    
    def __str__(self):
        return f"{self.nombre_plan} - ${self.precio}"

# ==========================================
# MODELO: CONTENIDO
# ==========================================
class Contenido(models.Model):
    TIPO_CHOICES = [
        ('pelicula', 'Película'),
        ('serie', 'Serie'),
        ('documental', 'Documental'),
    ]
    
    titulo = models.CharField(max_length=100, unique=True)
    tipo = models.CharField(max_length=20, choices=TIPO_CHOICES)
    fecha_lanzamiento = models.DateField()
    categoria = models.IntegerField()
    capitulos = models.CharField(max_length=5)
    
    # Relación 1 a muchos: una suscripción puede tener varios contenidos principales
    suscripcion = models.ForeignKey(
        Suscripcion, 
        on_delete=models.CASCADE,
        related_name='contenidos_principales'
    )

    class Meta:
        db_table = 'contenido'
    
    def __str__(self):
        return self.titulo

# ==========================================
# MODELO: USUARIO
# ==========================================
class Usuario(models.Model):
    nombre_usuario = models.CharField(max_length=50, unique=True)
    email = models.EmailField(max_length=254, unique=True)
    password = models.CharField(max_length=128)
    fecha_registro = models.DateField(auto_now_add=True)
    activo = models.BooleanField(default=True)
    
    # Relación 1 a muchos: una suscripción puede tener varios usuarios principales
    suscripcion = models.ForeignKey(
        Suscripcion, 
        on_delete=models.CASCADE,
        related_name='usuarios_principales'
    )

    class Meta:
        db_table = 'usuario'
    
    def __str__(self):
        return self.nombre_usuario
```

---

## 12. Procedimiento para realizar migraciones

1. Crear migraciones para la app:

```bash
python manage.py makemigrations app_Crunchyroll
```

2. Aplicar migraciones:

```bash
python manage.py migrate
```

> Si agregas `app_Crunchyroll` a `INSTALLED_APPS` después, vuelve a ejecutar `makemigrations` y `migrate`.

---

## 13. Empezar trabajando solo con `Suscripcion`

Por ahora definirás vistas y templates que solo manipulen el modelo `Suscripcion`.

---

## 14. `views.py` — funciones para Suscripción (CRUD)

Coloca este código en `app_Crunchyroll/views.py`. No usamos `forms.py` ni validaciones; usaremos `request.POST` y `redirect`.

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Suscripcion
from django.urls import reverse

# Inicio del sistema
def inicio_crunchyroll(request):
    return render(request, 'inicio.html')

# Agregar Suscripción (muestra formulario y crea)
def agregar_Suscripcion(request):
    if request.method == 'POST':
        nombre_plan = request.POST.get('nombre_plan')
        precio = request.POST.get('precio')
        calidad_video = request.POST.get('calidad_video')
        num_dispositivos = request.POST.get('num_dispositivos')
        beneficio_extra = request.POST.get('beneficio_extra')
        descarga_offline = True if request.POST.get('descarga_offline') == 'on' else False

        Suscripcion.objects.create(
            nombre_plan=nombre_plan,
            precio=precio,
            calidad_video=calidad_video,
            num_dispositivos=num_dispositivos,
            beneficio_extra=beneficio_extra,
            descarga_offline=descarga_offline
        )
        return redirect('ver_Suscripciones')

    return render(request, 'suscripciones/agregar_Suscripciones.html')

# Ver Suscripciones
def ver_Suscripciones(request):
    suscripciones = Suscripcion.objects.all()
    return render(request, 'suscripciones/ver_Suscripciones.html', {'suscripciones': suscripciones})

# Mostrar formulario de actualización
def actualizar_Suscripcion(request, suscripcion_id):
    sus = get_object_or_404(Suscripcion, id=suscripcion_id)
    return render(request, 'suscripciones/actualizar_Suscripciones.html', {'suscripcion': sus})

# Procesar actualización
def realizar_actualizacion_Suscripcion(request, suscripcion_id):
    sus = get_object_or_404(Suscripcion, id=suscripcion_id)
    if request.method == 'POST':
        sus.nombre_plan = request.POST.get('nombre_plan')
        sus.precio = request.POST.get('precio')
        sus.calidad_video = request.POST.get('calidad_video')
        sus.num_dispositivos = request.POST.get('num_dispositivos')
        sus.beneficio_extra = request.POST.get('beneficio_extra')
        sus.descarga_offline = True if request.POST.get('descarga_offline') == 'on' else False
        sus.save()
        return redirect('ver_Suscripciones')
    return redirect('actualizar_Suscripcion', suscripcion_id=sus.id)

# Borrar Suscripción
def borrar_Suscripcion(request, suscripcion_id):
    sus = get_object_or_404(Suscripcion, id=suscripcion_id)
    sus.delete()
    return redirect('ver_Suscripciones')
```

---

## 15. Crear carpeta `templates` dentro de `app_Crunchyroll`

Ruta: `app_Crunchyroll/templates/`.

Dentro crear:

* `base.html`
* `header.html`
* `navbar.html`
* `footer.html`
* `inicio.html`

Y la subcarpeta `suscripciones/` con las plantillas de CRUD (ver sección 21-22).

---

## 16–17. `base.html` + incluir Bootstrap (CSS y JS)

En `templates/base.html` usar el siguiente patrón (sencillo):

```html
<!doctype html>
<html lang="es">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>{% block title %}Crunchyroll Admin{% endblock %}</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <!-- Bootstrap Icons (para iconos) -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.5/font/bootstrap-icons.css" rel="stylesheet">
    {% block extra_head %}{% endblock %}
  </head>
  <body class="d-flex flex-column min-vh-100">
    {% include 'header.html' %}
    {% include 'navbar.html' %}

    <main class="container my-4">
      {% block content %}{% endblock %}
    </main>

    {% include 'footer.html' %}

    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    {% block extra_js %}{% endblock %}
  </body>
</html>
```

---

## 18. `navbar.html` con menú y submenús

Archivo `templates/navbar.html` con estructura solicitada (opciones principales con iconos; submenus sin iconos):

* Sistema de Administración Crunchyroll
* Inicio
* Suscripción -> (Agregar Suscripción, Ver Suscripción, Actualizar Suscripción, Borrar Suscripción)
* Usuarios -> (Agregar, Ver, Actualizar, Borrar) — *pendiente de implementar*
* Contenido -> (Agregar, Ver, Actualizar, Borrar) — *pendiente*

> Usar Bootstrap Collapse/Dropdown para submenús.

---

## 19. `footer.html`

Fijo al final, con derechos de autor, fecha del sistema y texto: "Creado por Victor Muñiz, Cbtis 128".

Ejemplo (en `templates/footer.html`):

```html
<footer class="mt-auto py-3 bg-light" style="position: sticky; bottom: 0;">
  <div class="container text-center small">
    &copy; {{ now|date:"Y" }} Crunchyroll Admin. Creado por Victor Muñiz, Cbtis 128.
  </div>
</footer>
```

(Nota: si quieres fijarlo siempre al fondo de la ventana, se puede usar CSS `position: fixed; bottom:0; width:100%`.)

---

## 20. `inicio.html`

Plantilla que hereda `base.html` y muestra información del sistema y una imagen tomada desde la red sobre "cinepolis" (usar `<img src="...">` con URL pública). Debe usar colores suaves y diseño simple.

---

## 21. Subcarpeta `suscripciones`

Ruta: `app_Crunchyroll/templates/suscripciones/`

Archivos que se crearán:

* `agregar_Suscripciones.html`
* `ver_Suscripciones.html` (tabla con botones ver, editar, borrar)
* `actualizar_Suscripciones.html`
* `Suscripciones.html` (opcional resumen)

---

## 22. Contenido de las plantillas de Suscripciones (resumen)

Las plantillas deben ser simples: usar formularios HTML con `method="post"` y `csrf_token`.

**ver_Suscripciones.html** mostrará una tabla con todas las suscripciones y botones para `Editar` (enlace a `actualizar_Suscripcion`), `Borrar` (enlace a `borrar_Suscripcion`) y `Ver` (puede redirigir a la misma página o a una vista detalle si se implementa).

**agregar_Suscripciones.html**: formulario para crear.

**actualizar_Suscripciones.html**: formulario pre-llenado con los datos de la suscripción.

> *No usar `forms.py` ni validac

### **18. En el archivo navbar.html incluir las opciones**

“Sistema de Administración Crunchyroll”, “Inicio”, “Suscripción” en submenu de Suscripcion (Agregar Suscripción, ver Suscripción, actualizar Suscripción, borrar Suscripción), “Usuarios” en submenu de Usuarios (Agregar Usuarios, ver Usuarios, actualizar Usuarios, borrar Usuarios), “Contenido” en submenu de Contenido (Agregar Contenido, ver Contenido, actualizar Contenido, borrar Contenido), incluir iconos a las opciones principales, no en los submenu.

---

### **19. En el archivo footer.html incluir derechos de autor, fecha del sistema y “Creado por Victor Muñiz, Cbtis 128” y mantenerla fija al final de la página.**

---

### **20. En el archivo inicio.html se usa para colocar información del sistema más una imagen tomada desde la red sobre cinepolis.**

---

### **21. Crear la subcarpeta Suscripciones dentro de app_Crunchyroll\templates.**

---

### **22. Crear los archivos html con su código correspondientes**

* agregar_Suscripciones.html
* ver_Suscripciones.html (mostrar en tabla con botones ver, editar, borrar)
* actualizar_Suscripciones.html
* Suscripciones.html

Dentro de `app_Crunchyroll\templates\suscripciones`.

---

### **23. No utilizar forms.py**

---

### **24. Procedimiento para crear el archivo urls.py en app_Crunchyroll**

---

### **25. Procedimiento para agregar app_Crunchyroll en settings.py de backend_Crunchyroll**

---

### **26. Realizar las configuraciones correspondiente a urls.py de backend_Crunchyroll**

---

### **27. Procedimiento para registrar los modelos en admin.py y volver a realizar las migraciones**

Después de registrar los modelos, agregar los **templates** siguientes:

---

#### **base.html**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block titulo %}Sistema Crunchyroll{% endblock %}</title>

    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

    {% include 'header.html' %}
</head>
<body class="bg-light text-dark">

    {% include 'navbar.html' %}

    <div class="container mt-4 mb-5">
        {% block contenido %}
        {% endblock %}
    </div>

    {% include 'footer.html' %}

    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

---

#### **header.html**

```html
<header class="bg-warning text-center p-3 shadow-sm">
    <h1 class="fw-bold text-dark">Sistema de Administración Crunchyroll</h1>
</header>
```

---

#### **navbar.html**

```html
<nav class="navbar navbar-expand-lg navbar-dark bg-dark shadow-sm">
  <div class="container-fluid">
    <a class="navbar-brand fw-bold" href="{% url 'inicio_crunchyroll' %}">
        <i class="bi bi-film"></i> Crunchyroll
    </a>

    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
      <span class="navbar-toggler-icon"></span>
    </button>

    <div class="collapse navbar-collapse" id="navbarNav">
      <ul class="navbar-nav me-auto mb-2 mb-lg-0">

        <li class="nav-item">
          <a class="nav-link" href="{% url 'inicio_crunchyroll' %}"><i class="bi bi-house-door"></i> Inicio</a>
        </li>

        <!-- Suscripción -->
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">
            <i class="bi bi-credit-card"></i> Suscripción
          </a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'agregar_Suscripcion' %}">Agregar Suscripción</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_Suscripcion' %}">Ver Suscripciones</a></li>
            <li><a class="dropdown-item" href="#">Actualizar Suscripción</a></li>
            <li><a class="dropdown-item" href="#">Borrar Suscripción</a></li>
          </ul>
        </li>

        <!-- Usuarios -->
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">
            <i class="bi bi-person-circle"></i> Usuarios
          </a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar Usuarios</a></li>
            <li><a class="dropdown-item" href="#">Ver Usuarios</a></li>
            <li><a class="dropdown-item" href="#">Actualizar Usuarios</a></li>
            <li><a class="dropdown-item" href="#">Borrar Usuarios</a></li>
          </ul>
        </li>

        <!-- Contenido -->
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">
            <i class="bi bi-collection-play"></i> Contenido
          </a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar Contenido</a></li>
            <li><a class="dropdown-item" href="#">Ver Contenido</a></li>
            <li><a class="dropdown-item" href="#">Actualizar Contenido</a></li>
            <li><a class="dropdown-item" href="#">Borrar Contenido</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

---

#### **footer.html**

```html
<footer class="bg-dark text-center text-white py-3 fixed-bottom">
    <p>© <script>document.write(new Date().getFullYear());</script> Creado por Victor Muñiz, Cbtis 128</p>
</footer>
```

---

#### **inicio.html**

```html
{% extends 'base.html' %}

{% block titulo %}Inicio - Crunchyroll{% endblock %}

{% block contenido %}
<div class="text-center">
    <h2 class="fw-bold mb-4">Bienvenido al Sistema de Administración Crunchyroll</h2>
    <p class="lead">Administra tus suscripciones, usuarios y contenidos fácilmente.</p>
    <img src="https://upload.wikimedia.org/wikipedia/commons/3/3d/Crunchyroll_Logo.png" alt="Crunchyroll" class="img-fluid rounded shadow mt-4" width="400">
</div>
{% endblock %}
```

---

#### **Suscripciones (dentro de templates/suscripciones)**

Archivos:

Perfecto. A continuación se incluyen **todos los archivos HTML** solicitados con su **estructura y código correspondiente**, siguiendo tus indicaciones exactamente y sin modificar nada de los puntos previos.

---

Aquí tienes la **estructura completa de archivos y carpetas** del proyecto **Crunchyroll (UIII_Crunchyroll_0659)** con Django, exactamente como debe quedar siguiendo todos los pasos indicados (del 1 al 31), incluyendo entorno virtual, proyecto, aplicación, templates y archivos principales.

---

## 📁 **Estructura completa del proyecto**

```
UIII_Crunchyroll_0659/
│
├── .venv/                                  # Carpeta del entorno virtual
│   ├── Scripts/
│   ├── Lib/
│   └── ...
│
├── backend_Crunchyroll/                    # Carpeta principal del proyecto Django
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py                         # Configuraciones del proyecto
│   ├── urls.py                             # Enlace a las rutas de las apps
│   ├── wsgi.py
│   └── manage.py (fuera de esta carpeta, raíz del proyecto)
│
├── app_Crunchyroll/                        # Aplicación principal del sistema
│   ├── __init__.py
│   ├── admin.py                            # Registro de modelos
│   ├── apps.py
│   ├── models.py                           # Modelos (Suscripcion, Usuario, Contenido)
│   ├── views.py                            # Vistas CRUD para Suscripcion
│   ├── urls.py                             # Rutas de la aplicación
│   ├── tests.py
│   │
│   ├── migrations/                         # Carpeta generada por las migraciones
│   │   ├── __init__.py
│   │   └── 0001_initial.py
│   │
│   └── templates/                          # Carpeta de plantillas HTML
│       ├── base.html
│       ├── header.html
│       ├── navbar.html
│       ├── footer.html
│       ├── inicio.html
│       │
│       └── suscripciones/                  # Carpeta específica para CRUD de Suscripciones
│           ├── agregar_Suscripciones.html
│           ├── ver_Suscripciones.html
│           ├── actualizar_Suscripciones.html
│           └── Suscripciones.html
│
├── db.sqlite3                              # Base de datos SQLite generada por Django
│
├── manage.py                               # Archivo principal de gestión del proyecto Django
│
└── requirements.txt             # Dependencias del proyecto (Django, etc.)
```
*(Aquí se colocan los CRUD correspondientes a las vistas definidas en views.py.)*

---

### **28. Utilizar colores suaves, atractivos y modernos, el código de las páginas web sencillas.**

---

### **29. No validar entrada de datos.**

---

### **30. Al inicio crear la estructura completa de carpetas y archivos.**

---

### **31. Proyecto totalmente funcional.**

---

### **32. Finalmente ejecutar servidor en el puerto 8032**

```bash
python manage.py runserver 8032
```
