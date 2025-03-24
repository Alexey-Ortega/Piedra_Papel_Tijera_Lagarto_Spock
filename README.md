import tkinter as tk
from tkinter import messagebox
import random

class PPTLS:
    def __init__(self, root):
        # Configuración básica
        self.root = root
        self.configurar_ventana()
        self.inicializar_variables()
        self.crear_interfaz()
    
    def configurar_ventana(self):
        """Configura la ventana principal"""
        self.root.title("Piedra, Papel, Tijeras, Lagarto, Spock")
        self.root.geometry("550x550")
        self.root.config(bg="#f0f0f0")
        self.root.resizable(True, True)
    
    def inicializar_variables(self):
        """Inicializa variables del juego"""
        self.puntuacion_jugador = 0
        self.puntuacion_computadora = 0
        self.historial = []
        
        # Definición de reglas
        self.reglas = {
            'piedra': {'vence': ['tijeras', 'lagarto'], 'mensaje': {'tijeras': 'Piedra aplasta Tijeras', 'lagarto': 'Piedra aplasta Lagarto'}},
            'papel': {'vence': ['piedra', 'spock'], 'mensaje': {'piedra': 'Papel cubre Piedra', 'spock': 'Papel refuta a Spock'}},
            'tijeras': {'vence': ['papel', 'lagarto'], 'mensaje': {'papel': 'Tijeras cortan Papel', 'lagarto': 'Tijeras decapitan Lagarto'}},
            'lagarto': {'vence': ['papel', 'spock'], 'mensaje': {'papel': 'Lagarto come Papel', 'spock': 'Lagarto envenena a Spock'}},
            'spock': {'vence': ['piedra', 'tijeras'], 'mensaje': {'piedra': 'Spock vaporiza Piedra', 'tijeras': 'Spock rompe Tijeras'}}
        }
        
        # Paleta de colores
        self.colores = {
            'piedra': "#FF5733",
            'papel': "#33A1FF",
            'tijeras': "#FFD700",
            'lagarto': "#2ECC71",
            'spock': "#9B59B6",
            'fondo': "#f0f0f0",
            'empate': "#808080",
            'victoria': "#2ECC71",
            'derrota': "#E74C3C"
        }
    
    def crear_interfaz(self):
        """Crea todos los elementos de la interfaz"""
        self.crear_encabezado()
        self.crear_panel_puntuacion()
        self.crear_botones_opciones()
        self.crear_panel_elecciones()
        self.crear_panel_resultados()
        self.crear_botones_control()
    
    def crear_encabezado(self):
        """Crea el título y descripción"""
        tk.Label(
            self.root, 
            text="¡Piedra, Papel, Tijeras, Lagarto, Spock!", 
            font=("Arial", 18, "bold"), 
            bg=self.colores['fondo']
        ).pack(pady=15)
        
        tk.Label(
            self.root, 
            text="Selecciona una opción y juega contra la computadora.", 
            font=("Arial", 12), 
            bg=self.colores['fondo']
        ).pack(pady=5)
    
    def crear_panel_puntuacion(self):
        """Crea el panel de puntuación"""
        frame = tk.Frame(self.root, bg=self.colores['fondo'])
        frame.pack(pady=10)
        
        self.label_puntuacion_jugador = tk.Label(
            frame, 
            text=f"Jugador: {self.puntuacion_jugador}", 
            font=("Arial", 14, "bold"), 
            bg=self.colores['fondo'], 
            fg="green"
        )
        self.label_puntuacion_jugador.grid(row=0, column=0, padx=20)
        
        self.label_puntuacion_computadora = tk.Label(
            frame, 
            text=f"Computadora: {self.puntuacion_computadora}", 
            font=("Arial", 14, "bold"), 
            bg=self.colores['fondo'], 
            fg="red"
        )
        self.label_puntuacion_computadora.grid(row=0, column=1, padx=20)
    
    def crear_botones_opciones(self):
        """Crea los botones para seleccionar opciones"""
        frame = tk.Frame(self.root, bg=self.colores['fondo'])
        frame.pack(pady=15)
        
        self.button_map = {}
        self.buttons = []
        
        opciones = ['piedra', 'papel', 'tijeras', 'lagarto', 'spock']
        for idx, opcion in enumerate(opciones):
            button = tk.Button(
                frame, 
                text=opcion.capitalize(), 
                width=9, 
                height=2, 
                font=("Arial", 12, "bold"), 
                bg=self.colores[opcion], 
                fg="white",
                command=lambda opcion=opcion: self.jugar(opcion)
            )
            row, col = idx // 3, idx % 3
            button.grid(row=row, column=col, padx=8, pady=8)
            self.button_map[opcion] = button
            self.buttons.append(button)
    
    def crear_panel_elecciones(self):
        """Crea el panel que muestra las elecciones actuales"""
        frame = tk.Frame(self.root, bg=self.colores['fondo'])
        frame.pack(pady=10)
        
        self.label_jugador = tk.Label(
            frame, 
            text="Tu elección: ", 
            font=("Arial", 12), 
            bg=self.colores['fondo'],
            width=20
        )
        self.label_jugador.grid(row=0, column=0, padx=5)
        
        self.label_computadora = tk.Label(
            frame, 
            text="Computadora: ", 
            font=("Arial", 12), 
            bg=self.colores['fondo'],
            width=20
        )
        self.label_computadora.grid(row=0, column=1, padx=5)
    
    def crear_panel_resultados(self):
        """Crea el panel que muestra los resultados"""
        self.label_resultado = tk.Label(
            self.root, 
            text="Elige una opción para comenzar.", 
            font=("Arial", 14, "bold"), 
            bg=self.colores['fondo'],
            height=2
        )
        self.label_resultado.pack(pady=10)
    
    def crear_botones_control(self):
        """Crea los botones de control (reiniciar, historial)"""
        frame = tk.Frame(self.root, bg=self.colores['fondo'])
        frame.pack(pady=10)
        
        tk.Button(
            frame, 
            text="Reiniciar Puntuación", 
            font=("Arial", 12), 
            bg="#E74C3C", 
            fg="white",
            command=self.reiniciar_puntuacion
        ).grid(row=0, column=0, padx=10)
        
        tk.Button(
            frame, 
            text="Ver Historial", 
            font=("Arial", 12), 
            bg="#3498DB", 
            fg="white",
            command=self.mostrar_historial
        ).grid(row=0, column=1, padx=10)
    
    def jugar(self, eleccion_jugador):
        """Maneja la lógica principal del juego"""
        eleccion_pc = random.choice(list(self.reglas.keys()))
        resultado, estado = self.determinar_ganador(eleccion_jugador, eleccion_pc)
        
        # Agregar al historial
        self.historial.append({
            'jugador': eleccion_jugador,
            'computadora': eleccion_pc,
            'resultado': resultado
        })
        
        # Actualizar interfaz
        self.actualizar_interfaz(eleccion_jugador, eleccion_pc, resultado, estado)
    
    def determinar_ganador(self, eleccion_jugador, eleccion_computadora):
        """Determina el ganador y retorna el mensaje y estado"""
        if eleccion_jugador == eleccion_computadora:
            return "¡Es un empate!", "empate"
        elif eleccion_computadora in self.reglas[eleccion_jugador]['vence']:
            mensaje = self.reglas[eleccion_jugador]['mensaje'][eleccion_computadora]
            self.puntuacion_jugador += 1
            return f"¡Ganaste! {mensaje}", "victoria"
        else:
            mensaje = self.reglas[eleccion_computadora]['mensaje'][eleccion_jugador]
            self.puntuacion_computadora += 1
            return f"Perdiste. {mensaje}", "derrota"
    
    def actualizar_interfaz(self, eleccion_jugador, eleccion_pc, resultado, estado):
        """Actualiza todos los elementos de la interfaz después de una jugada"""
        # Actualizar etiquetas de elección
        self.label_jugador.config(
            text=f"Tu elección: {eleccion_jugador.capitalize()}", 
            fg=self.colores[eleccion_jugador]
        )
        
        self.label_computadora.config(
            text=f"Computadora: {eleccion_pc.capitalize()}", 
            fg=self.colores[eleccion_pc]
        )
        
        # Actualizar resultado
        self.label_resultado.config(text=resultado, fg=self.colores[estado])
        
        # Actualizar puntuación
        self.label_puntuacion_jugador.config(text=f"Jugador: {self.puntuacion_jugador}")
        self.label_puntuacion_computadora.config(text=f"Computadora: {self.puntuacion_computadora}")
        
        # Resaltar botón seleccionado
        for button in self.buttons:
            button.config(relief="raised")
        self.button_map[eleccion_jugador].config(relief="sunken")
    
    def reiniciar_puntuacion(self):
        """Reinicia la puntuación y limpia la interfaz"""
        self.puntuacion_jugador = 0
        self.puntuacion_computadora = 0
        
        self.label_puntuacion_jugador.config(text=f"Jugador: {self.puntuacion_jugador}")
        self.label_puntuacion_computadora.config(text=f"Computadora: {self.puntuacion_computadora}")
        self.label_resultado.config(text="Puntuación reiniciada. Elige una opción.", fg="black")
        self.label_jugador.config(text="Tu elección: ", fg="black")
        self.label_computadora.config(text="Computadora: ", fg="black")
        
        # Restaurar botones
        for button in self.buttons:
            button.config(relief="raised")
    
    def mostrar_historial(self):
        """Muestra el historial de partidas en una ventana aparte"""
        # Si no hay historial, mostrar mensaje y salir
        if not self.historial:
            messagebox.showinfo("Historial de Partidas", "No hay partidas registradas.")
            return
        
        # Crear ventana para mostrar historial
        ventana_historial = tk.Toplevel(self.root)
        ventana_historial.title("Historial de Partidas")
        ventana_historial.geometry("400x400")
        ventana_historial.config(bg=self.colores['fondo'])
        
        # Título
        tk.Label(
            ventana_historial, 
            text="Historial de Partidas", 
            font=("Arial", 14, "bold"), 
            bg=self.colores['fondo']
        ).pack(pady=10)
        
        # Marco para el historial con scroll
        frame_container = tk.Frame(ventana_historial, bg=self.colores['fondo'])
        frame_container.pack(fill="both", expand=True, padx=10, pady=10)
        
        canvas = tk.Canvas(frame_container, bg=self.colores['fondo'])
        scrollbar = tk.Scrollbar(frame_container, orient="vertical", command=canvas.yview)
        frame_historial = tk.Frame(canvas, bg=self.colores['fondo'])
        
        # Configuración del scrollbar
        canvas.configure(yscrollcommand=scrollbar.set)
        scrollbar.pack(side="right", fill="y")
        canvas.pack(side="left", fill="both", expand=True)
        canvas.create_window((0, 0), window=frame_historial, anchor="nw")
        frame_historial.bind("<Configure>", lambda e: canvas.configure(scrollregion=canvas.bbox("all")))
        
        # Encabezados
        tk.Label(frame_historial, text="Ronda", font=("Arial", 12, "bold"), width=6, 
                bg=self.colores['fondo']).grid(row=0, column=0, padx=5, pady=5)
        tk.Label(frame_historial, text="Jugador", font=("Arial", 12, "bold"), width=8,
                bg=self.colores['fondo']).grid(row=0, column=1, padx=5, pady=5)
        tk.Label(frame_historial, text="PC", font=("Arial", 12, "bold"), width=8,
                bg=self.colores['fondo']).grid(row=0, column=2, padx=5, pady=5)
        tk.Label(frame_historial, text="Resultado", font=("Arial", 12, "bold"), width=18,
                bg=self.colores['fondo']).grid(row=0, column=3, padx=5, pady=5)
        
        # Datos
        for i, partida in enumerate(self.historial):
            tk.Label(frame_historial, text=str(i+1), font=("Arial", 10),
                    bg=self.colores['fondo']).grid(row=i+1, column=0, padx=5, pady=2)
            
            tk.Label(frame_historial, text=partida['jugador'].capitalize(), 
                    font=("Arial", 10), fg=self.colores[partida['jugador']],
                    bg=self.colores['fondo']).grid(row=i+1, column=1, padx=5, pady=2)
            
            tk.Label(frame_historial, text=partida['computadora'].capitalize(), 
                    font=("Arial", 10), fg=self.colores[partida['computadora']],
                    bg=self.colores['fondo']).grid(row=i+1, column=2, padx=5, pady=2)
            
            # Determinar color del resultado
            if "Ganaste" in partida['resultado']:
                color_res = self.colores['victoria']
            elif "Perdiste" in partida['resultado']:
                color_res = self.colores['derrota']
            else:
                color_res = self.colores['empate']
            
            resultado_corto = partida['resultado'].split('!')[0] if '!' in partida['resultado'] else partida['resultado'].split('.')[0]
            
            tk.Label(frame_historial, text=resultado_corto, 
                    font=("Arial", 10), fg=color_res,
                    bg=self.colores['fondo']).grid(row=i+1, column=3, padx=5, pady=2)

# Función para iniciar la aplicación
def main():
    ventana = tk.Tk()
    app = PPTLS(ventana)
    ventana.mainloop()

if __name__ == "__main__":
    main()
