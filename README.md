# PRACTICA-5-LOGIN-3

# Práctica 5 Login Parte 3 - Sesiones
En esta práctica, aprenderás a manejar sesiones de usuario en tu aplicación web utilizando Flask. Las sesiones te permiten almacenar información del usuario de manera persistente entre diferentes solicitudes HTTP, lo que es fundamental para construir aplicaciones web que requieren seguimiento del estado del usuario.

# Configuración inicial
Instalar Flask-Login:
Asegúrate de tener instalado Flask-Login en tu entorno virtual con el comando pip install flask_login.

# Importar clases y funciones necesarias en app.py:
Importa las siguientes clases y funciones de Flask-Login en tu archivo app.py:


from flask_login import LoginManager, login_user, logout_user, login_required
Crear instancia de LoginManager:
Crea una instancia de LoginManager asociada a tu aplicación Flask en app.py:


app = Flask(__name__)
db = MySQL(app)
login_manager = LoginManager(app)
Iniciar sesión y cerrar sesión
Actualizar la ruta /login:
Después de verificar que el usuario y la contraseña son válidos, utiliza login_user(logged_user) para iniciar la sesión del usuario.

# Crear método user_loader:
Crea un método user_loader que cargue los datos del usuario a partir de su id. Este método se utiliza para recuperar los datos del usuario en cada solicitud.

@login_manager.user_loader
def load_user(id):
    return ModelUsers.get_by_id(db, id)
# Agregar atributo is_active a la clase User:
Para mantener el control de la sesión, asegúrate de que la clase User tenga un atributo is_active. Para hacerlo, haz que la clase User herede de UserMixin de Flask-Login.

from flask_login import UserMixin

class User(UserMixin):
    ...
# Crear la ruta /logout:
Crea una ruta /logout que llame a logout_user() para cerrar la sesión del usuario y redirija a la página de inicio de sesión.


@app.route("/logout")
@login_required
def logout():
    logout_user()
    return redirect(url_for("login"))
# Proteger rutas y reconocer al usuario
Proteger rutas con el decorador @login_required:
Para restringir el acceso a ciertas rutas solo a usuarios autenticados, utiliza el decorador @login_required.


@app.route("/home")
@login_required
def home():
    return render_template("home.html")
# Proteger rutas solo para administradores:
Si deseas restringir el acceso a ciertas rutas solo para administradores, crea un decorador personalizado y úsalo en las rutas correspondientes.


from functools import wraps
from flask import abort
from flask_login import current_user

def admin_required(func):
    @wraps(func)
    def decorated_view(*args, **kwargs):
        if not current_user.is_authenticated or current_user.usertype != 1:
            abort(403)  # Acceso prohibido
        return func(*args, **kwargs)
    return decorated_view

@app.route("/admin")
@login_required
@admin_required
def admin():
    return render_template("admin.html")
Con estos pasos, habrás implementado el manejo de sesiones de usuario en tu aplicación web Flask, lo que te permitirá controlar el acceso a diferentes partes de tu aplicación basándote en la autenticación y el tipo de usuario.
