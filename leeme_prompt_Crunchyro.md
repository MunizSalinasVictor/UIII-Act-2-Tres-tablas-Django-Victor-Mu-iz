---

## **Primera parte**

**Proyecto:** Crunchyroll
**Lenguaje:** Python
**Framework:** Django
**Editor:** VS Code

---

### **1. Procedimiento para crear carpeta del Proyecto: `UIII_Crunchyroll_0659`**

1. Crear una carpeta desde el explorador de archivos con el nombre:
   **UIII_Crunchyroll_0659**
2. También se puede crear desde la terminal con el comando:

   ```bash
   mkdir UIII_Crunchyroll_0659
   ```

---

### **2. Procedimiento para abrir VS Code sobre la carpeta `UIII_Crunchyroll_0659`**

1. Abrir **VS Code**.
2. En el menú, seleccionar: **Archivo → Abrir carpeta...**
3. Seleccionar la carpeta **UIII_Crunchyroll_0659** y presionar **Aceptar**.
4. Alternativamente, desde la terminal:

   ```bash
   code UIII_Crunchyroll_0659
   ```

---

### **3. Procedimiento para abrir terminal en VS Code**

1. En VS Code, ir al menú superior: **Ver → Terminal**
2. Se abrirá una terminal integrada en la parte inferior del editor.

---

### **4. Procedimiento para crear carpeta entorno virtual “.venv” desde terminal de VS Code**

En la terminal de VS Code ejecutar:

```bash
python -m venv .venv
```

---

### **5. Procedimiento para activar el entorno virtual**

En la terminal, escribir según el sistema operativo:

* **Windows (PowerShell):**

  ```bash
  .venv\Scripts\Activate
  ```

* **Windows (cmd):**

  ```bash
  .venv\Scripts\activate.bat
  ```

* **Linux/Mac:**

  ```bash
  source .venv/bin/activate
  ```

---

### **6. Procedimiento para activar intérprete de python**

1. Presionar `Ctrl + Shift + P` en VS Code.
2. Escribir: **Python: Select Interpreter**
3. Seleccionar el intérprete que tenga la ruta del entorno virtual:

   ```
   .venv\Scripts\python.exe
   ```

---

### **7. Procedimiento para instalar Django**

En la terminal (con el entorno virtual activo) escribir:

```bash
pip install django
```

---

### **8. Procedimiento para crear proyecto backend_Crunchyroll sin duplicar carpeta**

En la terminal dentro de la carpeta del proyecto:

```bash
django-admin startproject backend_Crunchyroll .
```

El punto final evita que se cree una carpeta duplicada.

---

### **9. Procedimiento para ejecutar servidor en el puerto 8032**

En la terminal:

```bash
python manage.py runserver 8032
```

---

### **10. Procedimiento para copiar y pegar el link en el navegador**

1. Después de ejecutar el comando anterior, se mostrará un enlace como:

   ```
   http://127.0.0.1:8032/
   ```
2. Copiar y pegar ese enlace en el navegador.

---

### **11. Procedimiento para crear aplicacion app_Crunchyroll**

En la terminal:

```bash
python manage.py startapp app_Crunchyroll
```

---

### **12. Aqui el modelo models.py**

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

---

### **12.5 Procedimiento para realizar las migraciones (makemigrations y migrate)**

```bash
python manage.py makemigrations
python manage.py migrate
```

---

### **13. Primero trabajamos con el MODELO: Suscripcion**

---

### **14. En view de app_Crunchyroll crear las funciones con sus códigos correspondientes**

Funciones:

* inicio_crunchyroll
* agregar_Suscripcion
* actualizar_Suscripcion
* realizar_actualizacion_Suscripcion
* borrar_Suscripcion

---

### **15. Crear la carpeta “templates” dentro de “app_Crunchyroll”**

---

### **16. En la carpeta templates crear los archivos html (base.html, header.html, navbar.html, footer.html, inicio.html)**

---

### **17. En el archivo base.html agregar bootstrap para css y js**

---

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
