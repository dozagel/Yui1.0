import os
import json
from datetime import datetime

class YUIAssistant:
    def __init__(self):
        self.name = "YUI"
        self.user_name = "Tú"
        self.memory_file = "yui_memory.json"
        self.load_memory()
        self.show_welcome_screen()

    def load_memory(self):
        """Cargar memoria desde archivo JSON"""
        try:
            if os.path.exists(self.memory_file):
                with open(self.memory_file, 'r', encoding='utf-8') as f:
                    self.memory = json.load(f)
            else:
                self.memory = {
                    "conversaciones": [],
                    "frases_aprendidas": {
                        "hola": "¡Hola! ¿Cómo estás?",
                        "quién eres": "Soy YUI, tu asistente personal"
                    }
                }
        except:
            self.memory = {
                "conversaciones": [],
                "frases_aprendidas": {}
            }

    def save_memory(self):
        """Guardar memoria en archivo JSON"""
        with open(self.memory_file, 'w', encoding='utf-8') as f:
            json.dump(self.memory, f, ensure_ascii=False, indent=4)

    def show_welcome_screen(self):
        """Mostrar pantalla de bienvenida"""
        print("\n" * 3)
        print("✨ ASISTENTE YUI (Modo Entrenamiento) ✨")
        print("Comandos especiales:")
        print("- 'aprende': Para enseñarme nuevas respuestas")
        print("- 'olvida': Para eliminar algo que aprendí")
        print("- 'conocimiento': Ver todo lo que sé")
        print("- 'salir': Para terminar\n")
        self.wait_for_input()

    def wait_for_input(self):
        """Esperar entrada del usuario"""
        try:
            user_input = input("Tú: ").strip()
            self.process_input(user_input.lower())
        except EOFError:
            self.show_welcome_screen()

    def process_input(self, command):
        """Procesar el comando del usuario"""
        # Guardar en el historial
        self.memory["conversaciones"].append({
            "usuario": command,
            "fecha": datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        })
        
        # Comandos especiales
        if command.startswith("aprende"):
            self.learn_phrase(command)
        elif command.startswith("olvida"):
            self.forget_phrase(command)
        elif command == "conocimiento":
            self.show_knowledge()
        elif command == "salir":
            print(f"{self.name}: Guardando todo lo aprendido... ¡Hasta pronto! 👋")
            self.save_memory()
            return
        else:
            # Buscar en frases aprendidas
            response = self.memory["frases_aprendidas"].get(command)
            if response:
                print(f"{self.name}: {response}")
            else:
                print(f"{self.name}: No sé responder eso. ¿Quieres enseñarme? Usa 'aprende [frase]'")
            
            self.wait_for_input()

    def learn_phrase(self, command):
        """Aprender una nueva frase"""
        try:
            parts = command.split(" ", 1)
            if len(parts) > 1:
                phrase = parts[1]
                print(f"{self.name}: Por favor escribe cómo debo responder a '{phrase}':")
                response = input("Respuesta: ")
                self.memory["frases_aprendidas"][phrase] = response
                print(f"{self.name}: ¡Entendido! Ahora responderé '{response}' cuando digas '{phrase}'")
                self.save_memory()
            else:
                print(f"{self.name}: Formato incorrecto. Usa: aprende [tu frase]")
        except Exception as e:
            print(f"{self.name}: Error al aprender: {str(e)}")
        
        self.wait_for_input()

    def forget_phrase(self, command):
        """Olvidar una frase aprendida"""
        try:
            parts = command.split(" ", 1)
            if len(parts) > 1:
                phrase = parts[1]
                if phrase in self.memory["frases_aprendidas"]:
                    del self.memory["frases_aprendidas"][phrase]
                    print(f"{self.name}: He olvidado cómo responder a '{phrase}'")
                    self.save_memory()
                else:
                    print(f"{self.name}: No encontré esa frase en mi memoria")
            else:
                print(f"{self.name}: Formato incorrecto. Usa: olvida [frase a eliminar]")
        except Exception as e:
            print(f"{self.name}: Error al olvidar: {str(e)}")
        
        self.wait_for_input()

    def show_knowledge(self):
        """Mostrar todo el conocimiento adquirido"""
        if not self.memory["frases_aprendidas"]:
            print(f"{self.name}: Aún no he aprendido nada 😢")
        else:
            print(f"{self.name}: Esto es lo que sé responder:")
            for phrase, response in self.memory["frases_aprendidas"].items():
                print(f"📌 Cuando dices: '{phrase}'")
                print(f"   Yo respondo: '{response}'\n")
        
        self.wait_for_input()

if __name__ == "__main__":
    assistant = YUIAssistant()
