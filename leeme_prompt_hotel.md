🧱 1. Crear carpeta del proyecto

Abre una terminal (cmd o PowerShell) y ejecuta:

mkdir UIII_Hotel_1301
cd UIII_Hotel_1301


Esto crea la carpeta del proyecto y te posiciona dentro de ella.

💻 2. Abrir VS Code sobre la carpeta UIII_Hotel_1301

En la terminal escribe:

code .


Esto abrirá Visual Studio Code con la carpeta del proyecto abierta.

🧭 3. Abrir terminal en VS Code

En VS Code presiona:

Ctrl + ñ


o desde el menú:

Ver → Terminal

🐍 4. Crear entorno virtual .venv

En la terminal de VS Code escribe:

python -m venv .venv


Esto crea el entorno virtual dentro de tu carpeta del proyecto.

⚙️ 5. Activar el entorno virtual

En Windows:

.venv\Scripts\activate


En macOS/Linux:

source .venv/bin/activate


Cuando esté activo, verás algo como:

(.venv) C:\UIII_Hotel_1301>

🧩 6. Activar intérprete de Python en VS Code

En VS Code:

Pulsa Ctrl + Shift + P

Escribe: “Python: Select Interpreter”

Selecciona el que diga:
“.venv” (UIII_Hotel_1301)

🛠️ 7. Instalar Django

Ejecuta:

pip install django


Puedes verificar la instalación con:

django-admin --version

🧰 8. Crear proyecto sin duplicar carpeta

Ejecuta dentro de UIII_Hotel_1301:

django-admin startproject backend_Hotel .


(El punto . evita crear una subcarpeta adicional)

🚀 9. Ejecutar servidor en el puerto 1301
python manage.py runserver 1301

🌐 10. Copiar y pegar el link en el navegador

Abre en tu navegador:

http://127.0.0.1:1301/


Deberías ver la página de inicio de Django.

🏨 11. Crear aplicación app_Hotel
python manage.py startapp app_Hotel

🧾 12. Crear modelos en app_Hotel/models.py

Copia y pega este código corregido:

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

⚙️ 12.5. Migraciones
python manage.py makemigrations
python manage.py migrate

🗂️ 13. Crear modelo CATEGORÍA

Agrega al final de models.py:

class Categoria(models.Model):
    nombre = models.CharField(max_length=50)
    descripcion = models.TextField(blank=True, null=True)

    def __str__(self):
        return self.nombre


Vuelve a migrar:

python manage.py makemigrations
python manage.py migrate

🌄 14. Crear vistas en views.py

Dentro de app_Hotel/views.py agrega:

from django.shortcuts import render, redirect, get_object_or_404
from .models import Categoria

def inicio_hotel(request):
    return render(request, 'inicio.html')

def agregar_categoria(request):
    if request.method == 'POST':
        nombre = request.POST.get('nombre')
        descripcion = request.POST.get('descripcion')
        Categoria.objects.create(nombre=nombre, descripcion=descripcion)
        return redirect('ver_categorias')
    return render(request, 'categoria/agregar_categoria.html')

def ver_categorias(request):
    categorias = Categoria.objects.all()
    return render(request, 'categoria/ver_categorias.html', {'categorias': categorias})

def actualizar_categoria(request, id):
    categoria = get_object_or_404(Categoria, id=id)
    return render(request, 'categoria/actualizar_categoria.html', {'categoria': categoria})

def realizar_actualizacion_categoria(request, id):
    categoria = get_object_or_404(Categoria, id=id)
    if request.method == 'POST':
        categoria.nombre = request.POST.get('nombre')
        categoria.descripcion = request.POST.get('descripcion')
        categoria.save()
        return redirect('ver_categorias')
    return redirect('actualizar_categoria', id=id)

def borrar_categoria(request, id):
    categoria = get_object_or_404(Categoria, id=id)
    if request.method == 'POST':
        categoria.delete()
        return redirect('ver_categorias')
    return render(request, 'categoria/borrar_categoria.html', {'categoria': categoria})

🧱 15. Crear carpeta de plantillas
app_Hotel/
 └── templates/
     ├── base.html
     ├── header.html
     ├── navbar.html
     ├── footer.html
     └── inicio.html
