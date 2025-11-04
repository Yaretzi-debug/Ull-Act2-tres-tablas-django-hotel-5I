1. Procedimiento para crear carpeta del Proyecto: UIII_Hotel_1301
Abre tu terminal o línea de comandos y navega hasta la ubicación donde deseas guardar tu proyecto. Luego, ejecuta el siguiente comando:
code
Bash
mkdir UIII_Hotel_1301
2. Procedimiento para abrir VS Code sobre la carpeta UIII_Hotel_1301
Navega dentro de la carpeta que acabas de crear y abre VS Code:
code
Bash
cd UIII_Hotel_1301
code .
Esto abrirá VS Code con la carpeta UIII_Hotel_1301 como tu espacio de trabajo.
3. Procedimiento para abrir terminal en VS Code
Dentro de VS Code, puedes abrir la terminal yendo a Terminal > New Terminal en el menú superior, o usando el atajo de teclado Ctrl + (la tilde invertida).
4. Procedimiento para crear carpeta entorno virtual “.venv” desde terminal de VS Code
En la terminal de VS Code, ejecuta:
code
Bash
python -m venv .venv
Esto creará una carpeta llamada .venv dentro de tu proyecto, que contendrá un entorno virtual aislado para tus dependencias de Python.
5. Procedimiento para activar el entorno virtual
Para activar el entorno virtual, los comandos varían según tu sistema operativo:
Windows:
code
Bash
.venv\Scripts\activate
macOS/Linux:
code
Bash
source .venv/bin/activate
Una vez activado, verás (.venv) al principio de tu prompt de la terminal, indicando que el entorno virtual está activo.
6. Procedimiento para activar intérprete de python (dentro del entorno virtual)
Con el entorno virtual activado, Python ya estará usando el intérprete dentro de .venv. Para asegurarte y configurarlo en VS Code, puedes ir a la paleta de comandos (Ctrl+Shift+P o Cmd+Shift+P), buscar "Python: Select Interpreter" y elegir el que se encuentre en tu carpeta .venv.
7. Procedimiento para instalar Django
Con el entorno virtual activado, instala Django:
code
Bash
pip install Django
8. Procedimiento para crear proyecto backend_Hotel sin duplicar carpeta
En la raíz de tu proyecto UIII_Hotel_1301 (con el entorno virtual activado), ejecuta:
code
Bash
django-admin startproject backend_Hotel .
El . al final indica que el proyecto se creará en el directorio actual, evitando una carpeta anidada.
9. Procedimiento para ejecutar servidor en el puerto 1301
Navega a la carpeta donde se encuentra el archivo manage.py (que es la raíz de tu proyecto UIII_Hotel_1301) y ejecuta:
code
Bash
python manage.py runserver 1301
10. Procedimiento para copiar y pegar el link en el navegador
Cuando ejecutes el servidor, verás un mensaje como:
code
Code
Starting development server at http://127.0.0.1:1301/
Quit the server with CTRL-BREAK.
Copia la URL (por ejemplo, http://127.0.0.1:1301/) y pégala en tu navegador web. Deberías ver la página de bienvenida de Django.
11. Procedimiento para crear aplicación app_Hotel
Asegúrate de estar en la misma carpeta que manage.py (la raíz de UIII_Hotel_1301) y ejecuta:
code
Bash
python manage.py startapp app_Hotel
Esto creará la carpeta app_Hotel con los archivos básicos para la aplicación.
12. Aquí el modelo models.py
Aquí está el contenido de tu models.py para la aplicación app_Hotel. (Nota: Agregué el __str__ para Huesped correctamente indentado). Por ahora, nos centraremos en el modelo Categoria que definiremos más adelante, pero estos son los modelos que tenías.
app_Hotel/models.py
code
Python
from django.db import models

# ==========================================
# MODELO: EMPLEADOS
# ==========================================
class Empleado(models.Model):
    nombre = models.CharField(max_length=100)
    puesto = models.CharField(max_length=50)
    telefono = models.CharField(max_length=15)
    correo = models.EmailField(unique=True)
    fecha_contratacion = models.DateField()
    activo = models.BooleanField(default=True)

    def __str__(self):
        return f"{self.nombre} - {self.puesto}"

# ==========================================

# MODELO: HUÉSPEDES
# ==========================================
class Huesped(models.Model):
    nombre = models.CharField(max_length=100)
    telefono = models.CharField(max_length=15)
    correo = models.EmailField(unique=True)
    direccion = models.TextField(blank=True, null=True)
    fecha_registro = models.DateField(auto_now_add=True)
    def __str__(self):
        return self.nombre

# ==========================================
# MODELO: HABITACIONES
# ==========================================
class Habitacion(models.Model):
    numero_habitacion = models.PositiveIntegerField(unique=True)
    tipo = models.CharField(max_length=50)
    precio_noche = models.DecimalField(max_digits=8, decimal_places=2)
    disponible = models.BooleanField(default=True)
    descripcion = models.TextField(blank=True, null=True)
    huesped = models.ForeignKey(Huesped, on_delete=models.SET_NULL, null=True, blank=True, related_name="habitaciones")

    def __str__(self):
        return f"Habitación {self.numero_habitacion} - {self.tipo}"

# ==========================================
# MODELO: CATEGORÍA (Para Cinépolis, interpretando la instrucción 27)
# ==========================================
class Categoria(models.Model):
    nombre = models.CharField(max_length=100, unique=True)
    descripcion = models.TextField(blank=True, null=True)

    def __str__(self):
        return self.nombre
12.5 Procedimiento para realizar las migraciones (makemigrations y migrate)
Primero, asegúrate de haber guardado el archivo models.py con el nuevo modelo Categoria. Luego, en tu terminal (con el entorno virtual activado), ejecuta:
code
Bash
python manage.py makemigrations app_Hotel
python manage.py migrate
makemigrations crea los archivos de migración que le dicen a Django cómo crear las tablas en la base de datos, y migrate ejecuta esos archivos para crear las tablas reales.
13. Primero trabajamos con el MODELO: CATEGORÍA
Como se solicitó, nos enfocaremos en el modelo Categoria para las operaciones CRUD.
14. En views de app_Hotel crear las funciones con sus códigos correspondientes
Aquí están las funciones en app_Hotel/views.py para el CRUD de categorías. No usaremos forms.py según tu indicación, así que manejaremos los datos directamente de request.POST.
app_Hotel/views.py
code
Python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Categoria # Importamos solo Categoria por ahora

# Función de inicio del Hotel
def inicio_hotel(request):
    return render(request, 'app_Hotel/inicio.html')

# ==========================================
# FUNCIONES CRUD PARA CATEGORÍAS
# ==========================================

def agregar_categoria(request):
    if request.method == 'POST':
        nombre = request.POST.get('nombre')
        descripcion = request.POST.get('descripcion')
        Categoria.objects.create(nombre=nombre, descripcion=descripcion)
        return redirect('ver_categorias')
    return render(request, 'app_Hotel/categoria/agregar_categoria.html')

def ver_categorias(request):
    categorias = Categoria.objects.all()
    return render(request, 'app_Hotel/categoria/ver_categorias.html', {'categorias': categorias})

def actualizar_categoria(request, id):
    categoria = get_object_or_404(Categoria, pk=id)
    if request.method == 'POST':
        categoria.nombre = request.POST.get('nombre')
        categoria.descripcion = request.POST.get('descripcion')
        categoria.save()
        return redirect('ver_categorias')
    return render(request, 'app_Hotel/categoria/actualizar_categoria.html', {'categoria': categoria})

def borrar_categoria(request, id):
    categoria = get_object_or_404(Categoria, pk=id)
    if request.method == 'POST': # Confirmación de borrado
        categoria.delete()
        return redirect('ver_categorias')
    return render(request, 'app_Hotel/categoria/borrar_categoria.html', {'categoria': categoria})

# (Las funciones para Huespedes y Habitaciones se dejarán pendientes)
15. Crear la carpeta “templates” dentro de “app_Hotel”
Dentro de la carpeta app_Hotel, crea una nueva carpeta llamada templates.
16. En la carpeta templates crear los archivos html (base.html, header.html, navbar.html, footer.html, inicio.html)
Dentro de app_Hotel/templates, crea los siguientes archivos:
base.html
header.html
navbar.html
footer.html
inicio.html
17. En el archivo base.html agregar bootstrap para css y js
app_Hotel/templates/base.html
code
Html
{% load static %}
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Sistema Hotel{% endblock %}</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
    <!-- Font Awesome para iconos -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        body {
            display: flex;
            flex-direction: column;
            min-height: 100vh;
        }
        .content {
            flex: 1;
            padding-bottom: 70px; /* Espacio para el footer fijo */
        }
        .footer {
            position: fixed;
            bottom: 0;
            width: 100%;
            background-color: #343a40; /* Color de fondo del footer */
            color: white;
            text-align: center;
            padding: 15px 0;
            z-index: 1000;
        }
    </style>
</head>
<body>
    {% include 'app_Hotel/header.html' %}
    {% include 'app_Hotel/navbar.html' %}

    <div class="container mt-4 content">
        {% block content %}
        {% endblock %}
    </div>

    {% include 'app_Hotel/footer.html' %}

    <!-- Bootstrap JS y Popper.js -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
</body>
</html>
18. En el archivo navbar.html incluir las opciones
app_Hotel/templates/navbar.html
code
Html
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
    <div class="container-fluid">
        <a class="navbar-brand" href="{% url 'inicio_hotel' %}">
            <i class="fas fa-hotel"></i> Sistema de Administración Hotel
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                <li class="nav-item">
                    <a class="nav-link active" aria-current="page" href="{% url 'inicio_hotel' %}">
                        <i class="fas fa-home"></i> Inicio
                    </a>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownCategorias" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="fas fa-tags"></i> Categorías
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="navbarDropdownCategorias">
                        <li><a class="dropdown-item" href="{% url 'agregar_categoria' %}">Agregar Categoría</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_categorias' %}">Ver Categorías</a></li>
                        <!-- Los enlaces de actualizar y borrar se gestionarán desde la tabla de ver_categorias -->
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownHuespedes" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="fas fa-users"></i> Huéspedes
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="navbarDropdownHuespedes">
                        <li><a class="dropdown-item" href="#">Agregar Huéspedes</a></li>
                        <li><a class="dropdown-item" href="#">Ver Huéspedes</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Huéspedes</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Huéspedes</a></li>
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownHabitaciones" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="fas fa-bed"></i> Habitaciones
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="navbarDropdownHabitaciones">
                        <li><a class="dropdown-item" href="#">Agregar Habitación</a></li>
                        <li><a class="dropdown-item" href="#">Ver Habitaciones</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Habitación</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Habitación</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
</nav>
19. En el archivo footer.html incluir derechos de autor, fecha del sistema y “Creado por Cristal Yaretzi Tagle Pacheco, Cbtis 128” y mantenerla fija al final de la página.
app_Hotel/templates/footer.html
code
Html
<footer class="footer">
    <div class="container">
        <span class="text-light">&copy; {% now "Y" %} Sistema de Administración Hotel. Todos los derechos reservados.</span>
        <br>
        <span class="text-light">Creado por Cristal Yaretzi Tagle Pacheco, Cbtis 128</span>
    </div>
</footer>
20. En el archivo inicio.html se usa para colocar información del sistema más una imagen tomada desde la red sobre cinepolis.
app_Hotel/templates/inicio.html
code
Html
{% extends 'app_Hotel/base.html' %}

{% block title %}Inicio - Sistema Hotel{% endblock %}

{% block content %}
<div class="text-center my-5">
    <h1>Bienvenido al Sistema de Administración Cinépolis</h1>
    <p class="lead">Gestiona películas, horarios, salas y mucho más.</p>
</div>

<div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card shadow-sm">
            <div class="card-body">
                <h5 class="card-title">Acerca de Cinépolis</h5>
                <p class="card-text">Cinépolis es una cadena de cines mexicana, la más grande de América Latina y la cuarta a nivel mundial. Fundada en 1971, ha crecido hasta convertirse en un referente en la industria del entretenimiento, ofreciendo una experiencia cinematográfica de alta calidad con tecnología de punta y una amplia variedad de películas para todos los gustos.</p>
                <p class="card-text">Este sistema te ayudará a administrar de manera eficiente las categorías de películas, la información de los clientes (huéspedes) y la disponibilidad de salas (habitaciones), aunque nos centraremos en categorías por ahora.</p>
            </div>
        </div>
    </div>
</div>

<div class="text-center my-4">
    <p>¡Disfruta de la magia del cine!</p>
</div>

<div class="text-center my-5">
    <!-- Imagen de Cinépolis -->
    <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/1/1a/Cinepolis.svg/1200px-Cinepolis.svg.png" class="img-fluid rounded" alt="Logo de Cinépolis" style="max-height: 300px;">
</div>
{% endblock %}
21. Crear la subcarpeta carpeta categoria dentro de app_Hotel\templates.
Dentro de app_Hotel/templates, crea una nueva carpeta llamada categoria. La ruta será app_Hotel/templates/categoria.
22. Crear los archivos html con su código correspondientes de (agregar_categoria.html, ver_categorias.html mostrar en tabla con los botones ver, editar y borrar, actualizar_categoria.html, borrar_categoria.html) dentro de app_Hotel\templates\categoria.
app_Hotel/templates/categoria/agregar_categoria.html
code
Html
{% extends 'app_Hotel/base.html' %}

{% block title %}Agregar Categoría{% endblock %}

{% block content %}
<div class="container my-5">
    <div class="row justify-content-center">
        <div class="col-md-6">
            <div class="card shadow-lg p-3 mb-5 bg-body rounded">
                <div class="card-header bg-primary text-white text-center">
                    <h3 class="mb-0"><i class="fas fa-plus-circle"></i> Agregar Nueva Categoría</h3>
                </div>
                <div class="card-body">
                    <form method="post">
                        {% csrf_token %}
                        <div class="mb-3">
                            <label for="nombre" class="form-label">Nombre de la Categoría:</label>
                            <input type="text" class="form-control" id="nombre" name="nombre" required>
                        </div>
                        <div class="mb-3">
                            <label for="descripcion" class="form-label">Descripción:</label>
                            <textarea class="form-control" id="descripcion" name="descripcion" rows="3"></textarea>
                        </div>
                        <div class="d-grid gap-2">
                            <button type="submit" class="btn btn-success"><i class="fas fa-save"></i> Guardar Categoría</button>
                            <a href="{% url 'ver_categorias' %}" class="btn btn-secondary"><i class="fas fa-arrow-left"></i> Cancelar</a>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}
app_Hotel/templates/categoria/ver_categorias.html
code
Html
{% extends 'app_Hotel/base.html' %}

{% block title %}Ver Categorías{% endblock %}

{% block content %}
<div class="container my-5">
    <div class="d-flex justify-content-between align-items-center mb-4">
        <h2 class="mb-0"><i class="fas fa-tags"></i> Listado de Categorías</h2>
        <a href="{% url 'agregar_categoria' %}" class="btn btn-primary"><i class="fas fa-plus"></i> Nueva Categoría</a>
    </div>

    {% if categorias %}
    <div class="table-responsive">
        <table class="table table-hover table-striped shadow-sm">
            <thead class="bg-dark text-white">
                <tr>
                    <th scope="col">ID</th>
                    <th scope="col">Nombre</th>
                    <th scope="col">Descripción</th>
                    <th scope="col" class="text-center">Acciones</th>
                </tr>
            </thead>
            <tbody>
                {% for categoria in categorias %}
                <tr>
                    <td>{{ categoria.id }}</td>
                    <td>{{ categoria.nombre }}</td>
                    <td>{{ categoria.descripcion|default:"Sin descripción" }}</td>
                    <td class="text-center">
                        <a href="{% url 'actualizar_categoria' categoria.id %}" class="btn btn-warning btn-sm me-2" title="Editar"><i class="fas fa-edit"></i></a>
                        <a href="{% url 'borrar_categoria' categoria.id %}" class="btn btn-danger btn-sm" title="Borrar"><i class="fas fa-trash-alt"></i></a>
                    </td>
                </tr>
                {% endfor %}
            </tbody>
        </table>
    </div>
    {% else %}
    <div class="alert alert-info text-center" role="alert">
        No hay categorías registradas. <a href="{% url 'agregar_categoria' %}" class="alert-link">¡Agrega una ahora!</a>
    </div>
    {% endif %}
</div>
{% endblock %}
app_Hotel/templates/categoria/actualizar_categoria.html
code
Html
{% extends 'app_Hotel/base.html' %}

{% block title %}Actualizar Categoría{% endblock %}

{% block content %}
<div class="container my-5">
    <div class="row justify-content-center">
        <div class="col-md-6">
            <div class="card shadow-lg p-3 mb-5 bg-body rounded">
                <div class="card-header bg-warning text-dark text-center">
                    <h3 class="mb-0"><i class="fas fa-edit"></i> Actualizar Categoría</h3>
                </div>
                <div class="card-body">
                    <form method="post">
                        {% csrf_token %}
                        <div class="mb-3">
                            <label for="nombre" class="form-label">Nombre de la Categoría:</label>
                            <input type="text" class="form-control" id="nombre" name="nombre" value="{{ categoria.nombre }}" required>
                        </div>
                        <div class="mb-3">
                            <label for="descripcion" class="form-label">Descripción:</label>
                            <textarea class="form-control" id="descripcion" name="descripcion" rows="3">{{ categoria.descripcion }}</textarea>
                        </div>
                        <div class="d-grid gap-2">
                            <button type="submit" class="btn btn-success"><i class="fas fa-save"></i> Guardar Cambios</button>
                            <a href="{% url 'ver_categorias' %}" class="btn btn-secondary"><i class="fas fa-arrow-left"></i> Cancelar</a>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}
app_Hotel/templates/categoria/borrar_categoria.html
code
Html
{% extends 'app_Hotel/base.html' %}

{% block title %}Borrar Categoría{% endblock %}

{% block content %}
<div class="container my-5">
    <div class="row justify-content-center">
        <div class="col-md-6">
            <div class="card shadow-lg p-3 mb-5 bg-body rounded">
                <div class="card-header bg-danger text-white text-center">
                    <h3 class="mb-0"><i class="fas fa-trash-alt"></i> Confirmar Borrado</h3>
                </div>
                <div class="card-body">
                    <p class="card-text text-center lead">¿Estás seguro de que quieres borrar la categoría "<strong>{{ categoria.nombre }}</strong>"?</p>
                    <p class="text-muted text-center">Esta acción no se puede deshacer.</p>
                    <form method="post" class="text-center mt-4">
                        {% csrf_token %}
                        <button type="submit" class="btn btn-danger me-3"><i class="fas fa-trash-alt"></i> Sí, Borrar</button>
                        <a href="{% url 'ver_categorias' %}" class="btn btn-secondary"><i class="fas fa-ban"></i> Cancelar</a>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}
23. No utilizar forms.py.
Confirmado. Hemos manejado los datos directamente en las vistas usando request.POST.
24. Procedimiento para crear el archivo urls.py en app_Hotel con el código correspondiente para acceder a las funciones de views.py para operaciones de crud en categorias.
Dentro de la carpeta app_Hotel, crea un archivo llamado urls.py.
app_Hotel/urls.py
code
Python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_hotel, name='inicio_hotel'),
    # URLs para CRUD de Categorías
    path('categorias/', views.ver_categorias, name='ver_categorias'),
    path('categorias/agregar/', views.agregar_categoria, name='agregar_categoria'),
    path('categorias/actualizar/<int:id>/', views.actualizar_categoria, name='actualizar_categoria'),
    path('categorias/borrar/<int:id>/', views.borrar_categoria, name='borrar_categoria'),
]
25. Procedimiento para agregar app_Hotel en settings.py de backend_Hotel
Abre backend_Hotel/settings.py y busca la lista INSTALLED_APPS. Agrega 'app_Hotel' a esta lista.
backend_Hotel/settings.py (fragmento)
code
Python
# Application definition

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Hotel', # ¡Aquí está tu aplicación!
]
26. Realizar las configuraciones correspondiente a urls.py de backend_Hotel para enlazar con app_Hotel
Abre backend_Hotel/urls.py (el del proyecto principal) y enlaza las URLs de app_Hotel.
backend_Hotel/urls.py
code
Python
from django.contrib import admin
from django.urls import path, include # Importa include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Hotel.urls')), # Enlaza las URLs de app_Hotel
]
27. Procedimiento para registrar los modelos en admin.py y volver a realizar las migraciones.
Primero, registra Categoria en app_Hotel/admin.py.
app_Hotel/admin.py
code
Python
from django.contrib import admin
from .models import Empleado, Huesped, Habitacion, Categoria

# Registra tus modelos aquí.
admin.site.register(Empleado)
admin.site.register(Huesped)
admin.site.register(Habitacion)
admin.site.register(Categoria)
Volver a realizar las migraciones (si hiciste cambios en admin.py no es estrictamente necesario, pero por si acaso o por buenas prácticas si hubo cambios previos en modelos)
code
Bash
python manage.py makemigrations
python manage.py migrate
Si no hubo cambios en los modelos Empleado, Huesped, Habitacion o Categoria desde la última vez que ejecutaste makemigrations y migrate, es posible que no se generen nuevas migraciones.
28. Utilizar colores suaves, atractivos y modernos, el código de las páginas web sencillas.
Se ha intentado usar colores de Bootstrap (primary, success, warning, danger, dark, secondary) para darle un aspecto moderno y limpio a las páginas, y el código HTML es sencillo.
29. Al inicio crear la estructura completa de carpetas y archivos.
Ya se ha guiado a través de la creación de las carpetas y archivos esenciales. La estructura debería verse así:
code
Code
UIII_Hotel_1301/
├── .venv/
├── backend_Hotel/
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── app_Hotel/
│   ├── migrations/
│   │   └── __init__.py
│   ├── templates/
│   │   ├── categoria/
│   │   │   ├── agregar_categoria.html
│   │   │   ├── actualizar_categoria.html
│   │   │   ├── borrar_categoria.html
│   │   │   └── ver_categorias.html
│   │   ├── base.html
│   │   ├── header.html
│   │   ├── navbar.html
│   │   ├── footer.html
│   │   └── inicio.html
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   ├── urls.py
│   └── views.py
├── manage.py
└── db.sqlite3 (se crea después de las migraciones)
30. Proyecto totalmente funcional.
¡El esqueleto del proyecto para el CRUD de categorías está listo para ser funcional!
31. Finalmente ejecutar servidor en el puerto 1301
Desde la raíz de tu proyecto UIII_Hotel_1301 (donde está manage.py), y con el entorno virtual activado:
code
Bash
python manage.py runserver 1301
Ahora, visita http://127.0.0.1:1301/ en tu navegador. Deberías ver la página de inicio. Desde allí, el menú de navegación te permitirá acceder a la sección de "Categorías" para "Agregar Categoría" y "Ver Categorías".
