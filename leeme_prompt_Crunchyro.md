# Primera parte  
**Proyecto:** Crunchyroll  
**Lenguaje:** Python  
**Framework:** Django  
**Editor:** VS Code  

---

### 1. Procedimiento para crear carpeta del Proyecto  
Crear la carpeta con el nombre:  
`UIII_Crunchyroll_0659`

---

### 2. Procedimiento para abrir VS Code sobre la carpeta  
Abrir **Visual Studio Code**, luego seleccionar la carpeta:  
`UIII_Crunchyroll_0659`

---

### 3. Procedimiento para abrir terminal en VS Code  
En VS Code, abrir la terminal con:  
`Ctrl + ñ` o desde el menú **Terminal → Nueva terminal**

---

### 4. Procedimiento para crear carpeta entorno virtual “.venv” desde terminal de VS Code  
En la terminal ejecutar:  
```bash
python -m venv .venv
````

### 5. Procedimiento para activar el entorno virtual

En la terminal ejecutar:

```bash
.venv\Scripts\activate
```

---

### 6. Procedimiento para activar intérprete de Python

En VS Code, abrir la paleta de comandos con `Ctrl + Shift + P`, escribir:
`Python: Select Interpreter`
y seleccionar el entorno virtual `.venv`.

---

### 7. Procedimiento para instalar Django

Ejecutar en la terminal:

```bash
pip install django
```

---

### 8. Procedimiento para crear proyecto `backend_Crunchyroll` sin duplicar carpeta

Ejecutar en la terminal:

```bash
django-admin startproject backend_Crunchyroll .
```

---

### 9. Procedimiento para ejecutar servidor en el puerto 8032

Ejecutar:

```bash
python manage.py runserver 8032
```

---

### 10. Procedimiento para copiar y pegar el link en el navegador

Copiar el enlace mostrado en la terminal (ejemplo):

```
http://127.0.0.1:8032/
```

y pegarlo en el navegador.

---

### 11. Procedimiento para crear aplicación `app_Crunchyroll`

Ejecutar:

```bash
python manage.py startapp app_Crunchyroll
```

---

### 12. Archivo `models.py`

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

### 12.5 Procedimiento para realizar las migraciones

Ejecutar en la terminal:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

### 13. Trabajar primero con el MODELO: Suscripcion

---

### 14. En `views.py` de `app_Crunchyroll` crear las funciones

* inicio_crunchyroll
* agregar_Suscripcion
* actualizar_Suscripcion
* realizar_actualizacion_Suscripcion
* borrar_Suscripcion

---

### 15. Crear la carpeta “templates” dentro de “app_Crunchyroll”

---

### 16. En la carpeta templates crear los archivos HTML

* base.html
* header.html
* navbar.html
* footer.html
* inicio.html

---

### 17. En el archivo base.html agregar Bootstrap para CSS y JS

---

### 18. En el archivo navbar.html incluir las opciones

* Sistema de Administración Crunchyroll
* Inicio
* Suscripción

  * Agregar Suscripción
  * Ver Suscripción
  * Actualizar Suscripción
  * Borrar Suscripción
* Usuarios

  * Agregar Usuarios
  * Ver Usuarios
  * Actualizar Usuarios
  * Borrar Usuarios
* Contenido

  * Agregar Contenido
  * Ver Contenido
  * Actualizar Contenido
  * Borrar Contenido

Incluir iconos en las opciones principales, no en los submenús.

---

### 19. En el archivo footer.html incluir:

Derechos de autor, fecha del sistema y el texto:
**“Creado por Victor Muñiz, Cbtis 128”**
Debe mantenerse fijo al final de la página.

---

### 20. En el archivo inicio.html colocar información del sistema y una imagen de la red sobre cinepolis

---

### 21. Crear la subcarpeta “Suscripciones” dentro de `app_Crunchyroll/templates`

---

### 22. Crear los archivos HTML dentro de `app_Crunchyroll/templates/suscripciones`:

* agregar_Suscripciones.html
* ver_Suscripciones.html (mostrar en tabla con botones ver, editar y borrar)
* actualizar_Suscripciones.html
* Suscripciones.html

---

### 23. No utilizar forms.py

---

### 24. Crear el archivo `urls.py` en `app_Crunchyroll`

Incluir el código correspondiente para acceder a las funciones CRUD de `views.py`

---

### 25. Agregar `app_Crunchyroll` en `settings.py` de `backend_Crunchyroll`

---

### 26. Configurar `urls.py` de `backend_Crunchyroll` para enlazar con `app_Crunchyroll`

---

### 27. Registrar los modelos en `admin.py` y volver a realizar las migraciones

---

### 28. Por lo pronto solo trabajar con “Suscripciones”

Dejar pendiente los modelos **Usuarios** y **Contenido**

---

### 29. Utilizar colores suaves, atractivos y modernos

Diseñar las páginas con código sencillo.

---

### 30. No validar entrada de datos

---

### 31. Al inicio crear la estructura completa de carpetas y archivos

---

### 32. Proyecto totalmente funcional

---

### 33. Finalmente ejecutar servidor en el puerto 8032

```bash
python manage.py runserver 8032
```
