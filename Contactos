import csv
import os
import time
import re
from colorama import Fore, Style
from rich.console import Console
from rich.table import Table

# Consola para impresión con Rich
console = Console()

# Clase para representar un contacto
class Contacto:
    def __init__(self, nombre, telefono, email):
        self.nombre = nombre
        self.telefono = telefono
        self.email = email

    def __str__(self):
        return f"Nombre: {self.nombre}, Teléfono: {self.telefono}, Email: {self.email}"

# Clase para gestionar los contactos
class GestionContactos:
    def __init__(self, archivo="contactos.csv"):
        self.contactos = []
        self.archivo = archivo
        self.cargar_contactos()

    # Cargar contactos desde un archivo CSV
    def cargar_contactos(self):
        if not os.path.exists(self.archivo):
            self.mostrar_mensaje(f"El archivo '{self.archivo}' no existe. ¿Deseas crearlo ahora? (s/n)", "warning")
            if self.confirmar_accion():
                with open(self.archivo, "w", newline="") as file:
                    self.mostrar_mensaje(f"Archivo '{self.archivo}' creado con éxito.", "success")
            return

        try:
            with open(self.archivo, newline="") as file:
                reader = csv.reader(file)
                next(reader, None)  # Ignorar la primera línea (encabezados)
                for idx, row in enumerate(reader, 2):  # Comenzar en la línea 2 para mensajes de error
                    if len(row) != 3:
                        self.mostrar_mensaje(f"Error en línea {idx}: Formato inválido - {row}", "error")
                        continue
                    nombre, telefono, email = row
                    if not self.validar_telefono(telefono) or not self.validar_email(email):
                        self.mostrar_mensaje(f"Error en línea {idx}: Datos inválidos - {row}", "error")
                        continue
                    self.contactos.append(Contacto(nombre.strip(), telefono.strip(), email.strip()))
        except Exception as e:
            self.mostrar_mensaje(f"Error al abrir el archivo: {e}", "error")

    # Guardar contactos en un archivo CSV
    def guardar_contactos(self):
        try:
            with open(self.archivo, "w", newline="") as file:
                writer = csv.writer(file)
                writer.writerow(["Nombre", "Teléfono", "Email"])  # Escribir encabezados
                for contacto in self.contactos:
                    writer.writerow([contacto.nombre, contacto.telefono, contacto.email])
        except Exception as e:
            self.mostrar_mensaje(f"Error al guardar contactos: {e}", "error")

    # Validar formato de correo electrónico
    @staticmethod
    def validar_email(email):
        regex = r'^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$'
        return re.match(regex, email)

    # Validar formato de teléfono
    @staticmethod
    def validar_telefono(telefono):
        return telefono.isdigit() and len(telefono) >= 7

    # Confirmar acción del usuario
    @staticmethod
    def confirmar_accion():
        while True:
            opcion = input("Confirma tu acción (s/n): ").strip().lower()
            if opcion in ["s", "n"]:
                return opcion == "s"
            console.print("[bold red]Entrada inválida. Por favor, ingresa 's' o 'n'.[/bold red]")

    # Mostrar mensajes con estilos
    @staticmethod
    def mostrar_mensaje(mensaje, tipo="info"):
        colores = {
            "info": Fore.CYAN,
            "success": Fore.GREEN,
            "warning": Fore.YELLOW,
            "error": Fore.RED
        }
        print(colores.get(tipo, Fore.CYAN) + mensaje + Style.RESET_ALL)

    # Agregar un contacto
    def agregar_contacto(self):
        console.print("[bold cyan]Todos los campos son obligatorios.[/bold cyan]")
        nombre = input("Nombre: ").strip()
        telefono = input("Teléfono: ").strip()
        email = input("Correo Electrónico: ").strip()

        try:
            if not nombre or not telefono or not email:
                raise ValueError("Todos los campos son obligatorios.")
            if not self.validar_telefono(telefono):
                raise ValueError("El formato del teléfono no es válido. Debe contener solo números y al menos 7 dígitos.")
            if not self.validar_email(email):
                raise ValueError("El formato del correo electrónico no es válido.")
            if any(c.email == email for c in self.contactos):
                raise ValueError("El correo electrónico ya está registrado.")
            self.contactos.append(Contacto(nombre, telefono, email))
            self.guardar_contactos()
            self.mostrar_mensaje("Contacto agregado con éxito.", "success")
        except ValueError as e:
            self.mostrar_mensaje(f"Error: {e}", "error")

    # Mostrar todos los contactos con Rich
    def mostrar_contactos(self):
        if not self.contactos:
            self.mostrar_mensaje("No hay contactos disponibles.", "warning")
            return

        table = Table(title="Lista de Contactos")
        table.add_column("Nombre", style="cyan")
        table.add_column("Teléfono", style="magenta")
        table.add_column("Email", style="green")

        for contacto in self.contactos:
            table.add_row(contacto.nombre, contacto.telefono, contacto.email)

        console.print(table)

    # Buscar un contacto por nombre, teléfono o email
    def buscar_contacto(self):
        criterio = input("Introduce el nombre, teléfono o email a buscar: ").strip().lower()
        encontrados = [c for c in self.contactos if criterio in c.nombre.lower() or 
                       criterio in c.telefono or 
                       criterio in c.email.lower()]

        if not encontrados:
            self.mostrar_mensaje("No se encontraron contactos con ese criterio.", "warning")
        else:
            table = Table(title="Resultados de la Búsqueda")
            table.add_column("Nombre", style="cyan")
            table.add_column("Teléfono", style="magenta")
            table.add_column("Email", style="green")

            for contacto in encontrados:
                table.add_row(contacto.nombre, contacto.telefono, contacto.email)

            console.print(table)

    # Eliminar un contacto por nombre, teléfono o email
    def eliminar_contacto(self):
        criterio = input("Introduce el nombre, teléfono o email del contacto a eliminar: ").strip().lower()
        contactos_a_eliminar = [c for c in self.contactos if criterio in c.nombre.lower() or criterio in c.telefono or criterio in c.email.lower()]

        if not contactos_a_eliminar:
            self.mostrar_mensaje("No se encontró ningún contacto con ese criterio.", "error")
            return

        table = Table(title="Contactos a Eliminar")
        table.add_column("Nombre", style="cyan")
        table.add_column("Teléfono", style="magenta")
        table.add_column("Email", style="green")

        for contacto in contactos_a_eliminar:
            table.add_row(contacto.nombre, contacto.telefono, contacto.email)

        console.print(table)

        if self.confirmar_accion():
            self.contactos = [c for c in self.contactos if c not in contactos_a_eliminar]
            self.guardar_contactos()
            self.mostrar_mensaje("Contactos eliminados con éxito.", "success")
        else:
            self.mostrar_mensaje("Eliminación cancelada.", "warning")

    # Exportar contactos a un archivo CSV
    def exportar_contactos_csv(self):
        archivo_csv = input("Introduce el nombre del archivo CSV (sin extensión): ").strip()
        if not archivo_csv:
            self.mostrar_mensaje("El nombre del archivo no puede estar vacío.", "error")
            return
        if not archivo_csv.endswith(".csv"):
            archivo_csv += ".csv"
        try:
            with open(archivo_csv, "w", newline="") as file:
                writer = csv.writer(file)
                writer.writerow(["Nombre", "Teléfono", "Email"])  # Escribir encabezados
                for contacto in self.contactos:
                    writer.writerow([contacto.nombre, contacto.telefono, contacto.email])
            self.mostrar_mensaje(f"Contactos exportados con éxito a {archivo_csv}.", "success")
        except Exception as e:
            self.mostrar_mensaje(f"Error al exportar contactos: {e}", "error")

# Función para obtener una opción válida del menú
def obtener_opcion(menu_opciones):
    while True:
        opcion = input("Selecciona una opción: ").strip()
        if opcion.isdigit() and opcion in menu_opciones:
            return opcion
        console.print("[bold red]Opción inválida. Inténtalo de nuevo.[/bold red]")

# Función principal para interactuar con el usuario
def main():
    gestion = GestionContactos()

    menu_opciones = {
        "1": ("Agregar un contacto", gestion.agregar_contacto),
        "2": ("Mostrar todos los contactos", gestion.mostrar_contactos),
        "3": ("Buscar un contacto", gestion.buscar_contacto),
        "4": ("Eliminar un contacto", gestion.eliminar_contacto),
        "5": ("Exportar contactos a CSV", gestion.exportar_contactos_csv),
        "6": ("Salir", None)
    }

    while True:
        console.print("\n[bold cyan]--- Sistema de Gestión de Contactos ---[/bold cyan]")
        for key, (descripcion, _) in menu_opciones.items():
            console.print(f"[bold magenta]{key}[/bold magenta]. {descripcion}")

        opcion = obtener_opcion(menu_opciones)
        if opcion == "6":
            console.print("[bold cyan]Saliendo del sistema...[/bold cyan]")
            break

        _, funcion = menu_opciones[opcion]
        if funcion:
            funcion()

if __name__ == "__main__":
    main()