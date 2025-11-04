# Proyecto Crunchyroll - Guía de Implementación

## Especificaciones Técnicas
- **Lenguaje**: Python
- **Framework**: Django
- **Editor**: VS Code
- **Proyecto**: UIII_Crunchyroll_0659
- **Puerto**: 8032

---

## 1. Crear Carpeta del Proyecto
```bash
mkdir UIII_Crunchyroll_0659
```

## 2. Abrir VS Code en la Carpeta
```bash
cd UIII_Crunchyroll_0659
code .
```

## 3. Abrir Terminal en VS Code
- `Ctrl + Ñ` (Windows/Linux) o `Cmd + Ñ` (Mac)
- O menú: **View → Terminal**

## 4. Crear Entorno Virtual
```bash
python -m venv .venv
```

## 5. Activar Entorno Virtual
### Windows:
```bash
.venv\Scripts\activate
```
### Linux/Mac:
```bash
source .venv/bin/activate
```

## 6. Activar Intérprete de Python
- `Ctrl + Shift + P` → "Python: Select Interpreter"
- Seleccionar: `./.venv/Scripts/python.exe`

## 7. Instalar Django
```bash
pip install django
```

## 8. Crear Proyecto Django
```bash
django-admin startproject backend_Crunchyroll .
```

## 9. Ejecutar Servidor en Puerto 8032
```bash
python manage.py runserver 8032
```

## 10. Verificar en Navegador
- Abrir: `http://localhost:8032`

## 11. Crear Aplicación
```bash
python manage.py startapp app_Crunchyroll
```

---

## 12. Modelo `models.py`
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
    
    # Relación muchos a muchos
    contenidos_exclusivos = models.ManyToManyField(
        'Contenido', 
        related_name='suscripciones_exclusivas',
        blank=True
    )
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

## 12.5 Realizar Migraciones
```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 13-14. Views para Suscripciones

### `app_Crunchyroll/views.py`
```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Suscripcion

def inicio_crunchyroll(request):
    return render(request, 'inicio.html')

def agregar_Suscripcion(request):
    if request.method == 'POST':
        Suscripcion.objects.create(
            nombre_plan=request.POST['nombre_plan'],
            precio=request.POST['precio'],
            calidad_video=request.POST['calidad_video'],
            num_dispositivos=request.POST['num_dispositivos'],
            beneficio_extra=request.POST['beneficio_extra'],
            descarga_offline='descarga_offline' in request.POST
        )
        return redirect('ver_Suscripciones')
    return render(request, 'suscripciones/agregar_Suscripciones.html')

def ver_Suscripciones(request):
    suscripciones = Suscripcion.objects.all()
    return render(request, 'suscripciones/ver_Suscripciones.html', {'suscripciones': suscripciones})

def actualizar_Suscripcion(request, id):
    suscripcion = get_object_or_404(Suscripcion, id=id)
    if request.method == 'POST':
        suscripcion.nombre_plan = request.POST['nombre_plan']
        suscripcion.precio = request.POST['precio']
        suscripcion.calidad_video = request.POST['calidad_video']
        suscripcion.num_dispositivos = request.POST['num_dispositivos']
        suscripcion.beneficio_extra = request.POST['beneficio_extra']
        suscripcion.descarga_offline = 'descarga_offline' in request.POST
        suscripcion.save()
        return redirect('ver_Suscripciones')
    return render(request, 'suscripciones/actualizar_Suscripciones.html', {'suscripcion': suscripcion})

def borrar_Suscripcion(request, id):
    suscripcion = get_object_or_404(Suscripcion, id=id)
    suscripcion.delete()
    return redirect('ver_Suscripciones')
```

---

## 15-22. Estructura de Templates

### Estructura de Carpetas:
```
app_Crunchyroll/
├── templates/
│   ├── base.html
│   ├── header.html
│   ├── navbar.html
│   ├── footer.html
│   ├── inicio.html
│   └── suscripciones/
│       ├── agregar_Suscripciones.html
│       ├── ver_Suscripciones.html
│       ├── actualizar_Suscripciones.html
│       └── Suscripciones.html
```

### `templates/base.html`
```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Crunchyroll Admin</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            background-color: #f8f9fa;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        .navbar-custom {
            background: linear-gradient(135deg, #ff6b6b, #ee5a24);
        }
        .footer-custom {
            background: linear-gradient(135deg, #2c3e50, #34495e);
            color: white;
            position: fixed;
            bottom: 0;
            width: 100%;
        }
        .card {
            border: none;
            border-radius: 15px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            margin-bottom: 20px;
        }
    </style>
</head>
<body>
    {% include 'header.html' %}
    {% include 'navbar.html' %}
    
    <div class="container mt-4" style="margin-bottom: 100px;">
        {% block content %}
        {% endblock %}
    </div>
    
    {% include 'footer.html' %}
    
    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

### `templates/navbar.html`
```html
<nav class="navbar navbar-expand-lg navbar-dark navbar-custom">
    <div class="container">
        <a class="navbar-brand" href="{% url 'inicio_crunchyroll' %}">
            🎬 Sistema de Administración Crunchyroll
        </a>
        
        <div class="navbar-nav ms-auto">
            <a class="nav-link" href="{% url 'inicio_crunchyroll' %}">
                🏠 Inicio
            </a>
            
            <div class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                    📋 Suscripción
                </a>
                <ul class="dropdown-menu">
                    <li><a class="dropdown-item" href="{% url 'agregar_Suscripcion' %}">Agregar Suscripción</a></li>
                    <li><a class="dropdown-item" href="{% url 'ver_Suscripciones' %}">Ver Suscripción</a></li>
                    <li><a class="dropdown-item" href="#">Actualizar Suscripción</a></li>
                    <li><a class="dropdown-item" href="#">Borrar Suscripción</a></li>
                </ul>
            </div>
            
            <div class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                    👥 Usuarios
                </a>
                <ul class="dropdown-menu">
                    <li><a class="dropdown-item" href="#">Agregar Usuarios</a></li>
                    <li><a class="dropdown-item" href="#">Ver Usuarios</a></li>
                    <li><a class="dropdown-item" href="#">Actualizar Usuarios</a></li>
                    <li><a class="dropdown-item" href="#">Borrar Usuarios</a></li>
                </ul>
            </div>
            
            <div class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                    🎭 Contenido
                </a>
                <ul class="dropdown-menu">
                    <li><a class="dropdown-item" href="#">Agregar Contenido</a></li>
                    <li><a class="dropdown-item" href="#">Ver Contenido</a></li>
                    <li><a class="dropdown-item" href="#">Actualizar Contenido</a></li>
                    <li><a class="dropdown-item" href="#">Borrar Contenido</a></li>
                </ul>
            </div>
        </div>
    </div>
</nav>
```

### `templates/footer.html`
```html
<footer class="footer-custom text-center py-3">
    <div class="container">
        <p class="mb-0">
            &copy; {% now "Y" %} Crunchyroll Admin System | 
            Fecha: {% now "d/m/Y" %} | 
            Creado por Victor Muñiz, Cbtis 128
        </p>
    </div>
</footer>
```

### `templates/inicio.html`
```html
{% extends 'base.html' %}

{% block content %}
<div class="row">
    <div class="col-md-8 mx-auto">
        <div class="card">
            <div class="card-body text-center">
                <h2 class="card-title">Bienvenido al Sistema de Administración Crunchyroll</h2>
                <img src="https://images.unsplash.com/photo-1616530940355-351fabd9526b?ixlib=rb-4.0.3&auto=format&fit=crop&w=500&q=60" 
                     alt="Crunchyroll" class="img-fluid rounded my-4" style="max-height: 300px;">
                <p class="card-text">
                    Sistema de gestión para administrar suscripciones, usuarios y contenido 
                    de la plataforma Crunchyroll.
                </p>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

### `templates/suscripciones/agregar_Suscripciones.html`
```html
{% extends 'base.html' %}

{% block content %}
<div class="row">
    <div class="col-md-6 mx-auto">
        <div class="card">
            <div class="card-header bg-primary text-white">
                <h4>Agregar Nueva Suscripción</h4>
            </div>
            <div class="card-body">
                <form method="POST">
                    {% csrf_token %}
                    <div class="mb-3">
                        <label class="form-label">Nombre del Plan:</label>
                        <input type="text" name="nombre_plan" class="form-control" required>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Precio:</label>
                        <input type="number" step="0.01" name="precio" class="form-control" required>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Calidad de Video:</label>
                        <input type="text" name="calidad_video" class="form-control" required>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Número de Dispositivos:</label>
                        <input type="number" name="num_dispositivos" class="form-control" required>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Beneficios Extra:</label>
                        <textarea name="beneficio_extra" class="form-control" rows="3"></textarea>
                    </div>
                    <div class="mb-3 form-check">
                        <input type="checkbox" name="descarga_offline" class="form-check-input">
                        <label class="form-check-label">Descarga Offline</label>
                    </div>
                    <button type="submit" class="btn btn-success">Guardar Suscripción</button>
                    <a href="{% url 'ver_Suscripciones' %}" class="btn btn-secondary">Cancelar</a>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

### `templates/suscripciones/ver_Suscripciones.html`
```html
{% extends 'base.html' %}

{% block content %}
<div class="card">
    <div class="card-header bg-info text-white">
        <h4>Lista de Suscripciones</h4>
    </div>
    <div class="card-body">
        <a href="{% url 'agregar_Suscripcion' %}" class="btn btn-primary mb-3">➕ Agregar Nueva Suscripción</a>
        
        <div class="table-responsive">
            <table class="table table-striped table-hover">
                <thead class="table-dark">
                    <tr>
                        <th>ID</th>
                        <th>Nombre del Plan</th>
                        <th>Precio</th>
                        <th>Calidad Video</th>
                        <th>Dispositivos</th>
                        <th>Descarga Offline</th>
                        <th>Acciones</th>
                    </tr>
                </thead>
                <tbody>
                    {% for suscripcion in suscripciones %}
                    <tr>
                        <td>{{ suscripcion.id }}</td>
                        <td>{{ suscripcion.nombre_plan }}</td>
                        <td>${{ suscripcion.precio }}</td>
                        <td>{{ suscripcion.calidad_video }}</td>
                        <td>{{ suscripcion.num_dispositivos }}</td>
                        <td>
                            {% if suscripcion.descarga_offline %}
                                ✅ Sí
                            {% else %}
                                ❌ No
                            {% endif %}
                        </td>
                        <td>
                            <a href="{% url 'actualizar_Suscripcion' suscripcion.id %}" class="btn btn-warning btn-sm">✏️ Editar</a>
                            <a href="{% url 'borrar_Suscripcion' suscripcion.id %}" class="btn btn-danger btn-sm" 
                               onclick="return confirm('¿Estás seguro de eliminar esta suscripción?')">🗑️ Borrar</a>
                        </td>
                    </tr>
                    {% empty %}
                    <tr>
                        <td colspan="7" class="text-center">No hay suscripciones registradas</td>
                    </tr>
                    {% endfor %}
                </tbody>
            </table>
        </div>
    </div>
</div>
{% endblock %}
```

### `templates/suscripciones/actualizar_Suscripciones.html`
```html
{% extends 'base.html' %}

{% block content %}
<div class="row">
    <div class="col-md-6 mx-auto">
        <div class="card">
            <div class="card-header bg-warning text-dark">
                <h4>Actualizar Suscripción</h4>
            </div>
            <div class="card-body">
                <form method="POST">
                    {% csrf_token %}
                    <div class="mb-3">
                        <label class="form-label">Nombre del Plan:</label>
                        <input type="text" name="nombre_plan" class="form-control" 
                               value="{{ suscripcion.nombre_plan }}" required>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Precio:</label>
                        <input type="number" step="0.01" name="precio" class="form-control" 
                               value="{{ suscripcion.precio }}" required>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Calidad de Video:</label>
                        <input type="text" name="calidad_video" class="form-control" 
                               value="{{ suscripcion.calidad_video }}" required>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Número de Dispositivos:</label>
                        <input type="number" name="num_dispositivos" class="form-control" 
                               value="{{ suscripcion.num_dispositivos }}" required>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Beneficios Extra:</label>
                        <textarea name="beneficio_extra" class="form-control" rows="3">{{ suscripcion.beneficio_extra }}</textarea>
                    </div>
                    <div class="mb-3 form-check">
                        <input type="checkbox" name="descarga_offline" class="form-check-input" 
                               {% if suscripcion.descarga_offline %}checked{% endif %}>
                        <label class="form-check-label">Descarga Offline</label>
                    </div>
                    <button type="submit" class="btn btn-success">Actualizar Suscripción</button>
                    <a href="{% url 'ver_Suscripciones' %}" class="btn btn-secondary">Cancelar</a>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

---

## 24. URLs de la Aplicación

### `app_Crunchyroll/urls.py`
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_crunchyroll, name='inicio_crunchyroll'),
    path('suscripciones/agregar/', views.agregar_Suscripcion, name='agregar_Suscripcion'),
    path('suscripciones/ver/', views.ver_Suscripciones, name='ver_Suscripciones'),
    path('suscripciones/actualizar/<int:id>/', views.actualizar_Suscripcion, name='actualizar_Suscripcion'),
    path('suscripciones/borrar/<int:id>/', views.borrar_Suscripcion, name='borrar_Suscripcion'),
]
```

---

## 25. Configurar Settings

### `backend_Crunchyroll/settings.py`
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Crunchyroll',  # Agregar esta línea
]
```

---

## 26. URLs del Proyecto

### `backend_Crunchyroll/urls.py`
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Crunchyroll.urls')),
]
```

---

## 27. Registrar Modelos en Admin

### `app_Crunchyroll/admin.py`
```python
from django.contrib import admin
from .models import Suscripcion, Contenido, Usuario

@admin.register(Suscripcion)
class SuscripcionAdmin(admin.ModelAdmin):
    list_display = ['nombre_plan', 'precio', 'calidad_video', 'num_dispositivos']

@admin.register(Contenido)
class ContenidoAdmin(admin.ModelAdmin):
    list_display = ['titulo', 'tipo', 'fecha_lanzamiento']

@admin.register(Usuario)
class UsuarioAdmin(admin.ModelAdmin):
    list_display = ['nombre_usuario', 'email', 'fecha_registro']
```

### Realizar Migraciones Nuevamente:
```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 31. Ejecutar Servidor Final
```bash
python manage.py runserver 8032
```

---

## Estructura Final del Proyecto:
```
UIII_Crunchyroll_0659/
├── .venv/
├── backend_Crunchyroll/
│   ├── settings.py
│   ├── urls.py
│   └── ...
├── app_Crunchyroll/
│   ├── templates/
│   │   ├── base.html
│   │   ├── header.html
│   │   ├── navbar.html
│   │   ├── footer.html
│   │   ├── inicio.html
│   │   └── suscripciones/
│   │       ├── agregar_Suscripciones.html
│   │       ├── ver_Suscripciones.html
│   │       ├── actualizar_Suscripciones.html
│   │       └── Suscripciones.html
│   ├── admin.py
│   ├── models.py
│   ├── views.py
│   ├── urls.py
│   └── ...
├── db.sqlite3
├── manage.py
└── requirements.txt
```

El proyecto estará completamente funcional en `http://localhost:8032` con todas las operaciones CRUD para Suscripciones implementadas.
