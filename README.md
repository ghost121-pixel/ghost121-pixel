import json

class Juego:
    def __init__(self, id_juego, nombre, precio, stock, categoria):
        self.id_juego = id_juego
        self.nombre = nombre
        self.precio = precio
        self.stock = stock
        self.categoria = categoria
        self.reseñas = []

    def agregar_reseña(self, reseña):
        self.reseñas.append(reseña)

    def __str__(self):
        return f"{self.nombre} - ${self.precio} (Stock: {self.stock}, Categoría: {self.categoria})"

class Tienda:
    def __init__(self):
        self.juegos = {}
        self.carrito = []

    def agregar_juego(self, juego):
        self.juegos[juego.id_juego] = juego

    def mostrar_juegos(self):
        print("Juegos disponibles:")
        for juego in self.juegos.values():
            print(juego)

    def buscar_juego(self, nombre):
        for juego in self.juegos.values():
            if nombre.lower() in juego.nombre.lower():
                print(juego)

    def agregar_al_carrito(self, id_juego, cantidad):
        if id_juego in self.juegos:
            juego = self.juegos[id_juego]
            if juego.stock >= cantidad:
                self.carrito.append((juego, cantidad))
                juego.stock -= cantidad
                print(f"{cantidad} de {juego.nombre} agregado al carrito.")
            else:
                print("No hay suficiente stock.")
        else:
            print("Juego no encontrado.")

    def mostrar_carrito(self):
        print("Carrito de compras:")
        for juego, cantidad in self.carrito:
            print(f"{juego.nombre} x {cantidad}")

    def total_carrito(self):
        total = sum(juego.precio * cantidad for juego, cantidad in self.carrito)
        print(f"Total a pagar: ${total}")

    def guardar_juegos(self, archivo):
        with open(archivo, 'w') as f:
            json.dump({juego.id_juego: juego.__dict__ for juego in self.juegos.values()}, f)

    def cargar_juegos(self, archivo):
        try:
            with open(archivo, 'r') as f:
                juegos_data = json.load(f)
                for juego_data in juegos_data.values():
                    juego = Juego(juego_data['id_juego'], juego_data['nombre'], juego_data['precio'],
                                  juego_data['stock'], juego_data['categoria'])
                    self.juegos[juego.id_juego] = juego
        except FileNotFoundError:
            print("Archivo no encontrado. Cargando juegos vacíos.")

class Usuario:
    def __init__(self, nombre, contraseña):
        self.nombre = nombre
        self.contraseña = contraseña

class SistemaAutenticacion:
    def __init__(self):
        self.usuarios = {}

    def registrar_usuario(self, nombre, contraseña):
        if nombre not in self.usuarios:
            self.usuarios[nombre] = Usuario(nombre, contraseña)
            print("Usuario registrado con éxito.")
        else:
            print("El usuario ya existe.")

    def autenticar_usuario(self, nombre, contraseña):
        usuario = self.usuarios.get(nombre)
        if usuario and usuario.contraseña == contraseña:
            print("Autenticación exitosa.")
            return True
        else:
            print("Nombre de usuario o contraseña incorrectos.")
            return False

# Ejemplo de uso
tienda = Tienda()
sistema_autenticacion = SistemaAutenticacion()

# Registro de usuarios
sistema_autenticacion.registrar_usuario("usuario1", "contraseña1")
sistema_autenticacion.registrar_usuario("usuario2", "contraseña2")

# Autenticación de un usuario
if sistema_autenticacion.autenticar_usuario("usuario1", "contraseña1"):
    # Cargar juegos desde un archivo
    tienda.cargar_juegos("juegos.json")

    # Agregar juegos a la tienda
    tienda.agregar_juego(Juego(1, "Juego A", 29.99, 10, "Acción"))
    tienda.agregar_juego(Juego(2, "Juego B", 49.99, 5, "Aventura"))
    tienda.agregar_juego(Juego(3, "Juego C", 19.99, 15, "Deportes"))

    # Mostrar juegos disponibles
    tienda.mostrar_juegos()

    # Buscar un juego
    print("\nBuscando 'Juego A':")
    tienda.buscar_juego("Juego A")

    # Agregar juegos al carrito
    tienda.agregar_al_carrito(1, 2)
    tienda.agregar_al_carrito(2, 1)

    # Mostrar carrito y total
    tienda.mostrar_carrito()
    tienda.total_carrito()

    # Guardar juegos en un archivo
    tienda.guardar_juegos("juegos.json")
