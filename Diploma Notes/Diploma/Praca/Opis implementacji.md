# Opis implementacji
Ten rozdział opisuje sposób implementacji dodatku. Rozpatrywany kod dla przejrzystości został podzielony na dwie części:
- kod łączący Blendera z dodatkiem
-  gramatykę i odpowiadającą implementację języka OpenSCAD

#### Integracja API
W tej części pracy omówiony zostanie kod specyficzny dla umożliwienia dodania dodatku do Blendera. 
``` python
bl_info = {
	"name": "Add-on SCAD",
	"blender": (3, 2, 0),
	"category": "Interface",
	"author": "Paul Kokot",
	"location": "Text Editor -> Header",
	"version": (1, 2, 10)
}
```
Powyższy kod zawiera metadane dodatku, które pozwolą na łatwiejsze znalezienie w liście innych dodatków, a także przedstawi podstawowe informacje o autorze, wersji dodatku, wersji blendera, z którym dodatek jest kompatybilny, a także kategorię. Możliwe jest również umieszczenie strony kontaktowej z autorem dodatku i linku do dokumentacji.

``` python 
class TEXT_OT_run_scad(bpy.types.Operator):
	"""Runs SCAD script"""
	bl_idname = "text.run_scad"
	bl_label = "Run SCAD"
	bl_options = {'REGISTER', 'UNDO'}
	
	def execute(self, context):
		st = context.space_data
		text = st.text
		parse_openscad(text.as_string())
		return {'FINISHED'}
```
Każda dodana operacja w Blenderze musi zostać zdefiniowana jako klasa, według konkretnego wzoru, jak i nazewnictwa. Powyższa klasa sugeruje, że operacja będzie operować na edytorze tekstowaym blendera, jest rejestrowalna i cofalna.
Funkcja execute uruchamia funkcję parse_openscad na tekście umieszczonym w edytorze tekstowym, na którym została ona wykonana.

``` python 
def draw(self, context):
	self.layout.operator("text.run_scad", text="", icon='MONKEY')
  
addon_keymaps = []

# Register and unregister add-on
def register():``
	bpy.utils.register_class(TEXT_OT_run_scad)
	bpy.types.TEXT_HT_header.append(draw)
	
	#Add hotkey
	wm = bpy.context.window_manager
	kc = wm.keyconfigs.addon
	if kc:
		km = wm.keyconfigs.addon.keymaps.new(name='Run OpenSCAD mode', space_type='TEXT_EDITOR')
		kmi = km.keymap_items.new(TEXT_OT_run_scad.bl_idname, 'T', 'PRESS', ctrl=True, shift=True)
		addon_keymaps.append((km, kmi))

def unregister():
	# Remove the hotkey
	for km, kmi in addon_keymaps:
		km.keymap_items.remove(kmi)
		addon_keymaps.clear()
	
	bpy.utils.unregister_class(TEXT_OT_run_scad)
	bpy.types.TEXT_HT_header.remove(draw)
```
W powyższym kodzie zdefiniowane są dwie funkcje register i unregister, które wykonywane są odpowiednio podczas aktywowania i dezaktywowania dodatku. Podczas rejestracji rejestrowana jest klasa operatora, dodany przycisk do interface użytkownika pozwalający na uruchomienie operatora, a także dodany jest skrót klawiszowy, na kombinację klawiszy CTRL + SHIFT + T, z tą samą funkconalnością.
W funckji unregister kolejność wyrejestrowywania jest odwrotna, co pozwala uniknąć konfliktów. W pierwszej kolejności usuwane są skróty klawiszowe, a następnie wyrejestrowywana jest klasa i przycisk na interfejsie użytkownika
#### Implementacja języka OpenSCAD
``` ebnf
start: instruction+

instruction: action ";"
| action operator+ ";"
| IDENTIFIER "=" (expression) ";" -> assign
| "{" instruction+ "}" operator+ ";" -> block

action: OBJECT "(" (INTEGER | FLOAT | vector | IDENTIFIER) ")"
| SPECIAL

operator: TRANSFORM "(" (INTEGER | FLOAT | vector | IDENTIFIER)")" -> transform
| "color" "(" (string | INTEGER | FLOAT | vector | IDENTIFIER) ")" -> color
  
vector: "[" (INTEGER | FLOAT) ["," (INTEGER | FLOAT)]+"]"

expression: (INTEGER | FLOAT | vector | string | IDENTIFIER) -> value
| "-" expression -> negation
| expression "+" expression -> addition
| expression "-" expression -> subtraction
| expression "*" expression -> multiplication
| expression "/" expression -> division
| "(" expression ")" -> parentheses
  
OBJECT: "cube"|"sphere"|"cylinder"|"monkey"|"uvsphere"|"cone"|"torus"|"icosphere"
TRANSFORM: "translate"|"rotate"|"scale"
SPECIAL: "clear"

string : ESCAPED_STRING
%import common.WORD -> IDENTIFIER
%import common.INT -> INTEGER
%import common.FLOAT -> FLOAT
%import common.ESCAPED_STRING
%import common.WS
%ignore WS
%import common.SH_COMMENT
%ignore SH_COMMENT
```
Powyższy kod przedstawia definicję gramatyki języka bazowanego na języku OpenSCAD. Definicja gramatyki została wykonana w EBNF (Extended Backus-Naur Form)

Sposób implementacji funkcjonalności języka OpenSCAD opiera się na analizie Tokenów wygenerowanych w procesie parsowania przez moduł Lark.
``` python 
def parse_openscad(text):
	openscad_parser = Lark(r"""
	start: instruction+
	...
	""", parser='earley')
	
	for inst in openscad_parser.parse(text).children:
		run_instruction(inst)
```
Funkcja parse_openscad() parsuje text w formacie EBNF za pomocą algorytmu Earleya, a następnie iteruje przez wszystkie Tokeny i wykonuje funkcję run_instruction() na każdym z nich.

![Start|300](file:///home/paul/diploma/Images/diagram/start.png)
Punkt startowy analizy tekstu, sprawdza jedną lub więcej instrukcji

``` python 
def run_instruction(t):
	if(t.data == 'assign'):
		assign_implementation(t)
	elif t.data == "block" or t.data == "instruction":
		block_implementation(t)
	elif t.data == "color":
		color_implementation(t)
	elif t.data == "action":
		action_implementation(t)
	elif t.data == "transform":
		transform_implementation(t)
	else:
		raise SyntaxError('Unknown instruction: %s' % t.data)
```
funkcja run_instruction() ustala z jakim rodzajem instrukcji znajduje się w Tokenie t. Możliwe opcje to akcje takie jak utworzenie prymitywa, operator coloru, operator transformacji, przypisanie ekspresji do identyfikatora lub instrukcję blokową.
![instruction.png](file:///home/paul/diploma/Images/diagram/instruction.png)
Diagram przedstawiający instrukcję. Instrukcja zawsze zakończona jest średnikiem.
``` python 
def assign_implementation(t):
	if type(t.children[1]) is Tree:
		res = expression_breakdown(t.children[1])
		variables[t.children[0]] = res
		print(f"assigning {t.children[0]} value {res}")
	else:
		variables[t.children[0]] = t.children[1]
		print(f"assigning {t.children[0]} value {t.children[1]}")
```
Funkcja assign_implementation() defniniuje operację przypisania. Do słownika "variables" przypisywana jest wartość lub wektor z odpowiednim identyfikatorem.

![operator.png](file:///home/paul/diploma/Images/diagram/operator.png)
Operator definiuje jakie operacje są możliwe do zaaplikowania na objektach. Są to color, albo Transform.
![TRANSFORM.png](file:///home/paul/diploma/Images/diagram/TRANSFORM.png)
Operacja Transform dzieli się dalej na trzy operacje: translate, która zmienia położenie objektów, rotate - zmieniający rotację i scale wpływający na ich skalę.

Wartości przyjmowane przez operator to
- Integer - liczba całkowita
- Float - liczba zmiennoprzecinkowa
- Vector - wielowymiarowa tablica
  ![vector.png](file:///home/paul/diploma/Images/diagram/vector.png)
  Wektor to wielowymiarowa tablica zamknięta w kwadratowych nawiasach, składowe wektora to liczby zmiennoprzecinkowe i całkowite, przedzielone przecinkami
  - identifier - zmienna przypisana wcześniej i znajdująca się w słowniku zmiennych
  - string - wyłącznie w przypadku coloru, mogą to być wartości słownie opisujące kolor takie jak "blue" - kolor niebieski lub "red" - kolor czerwony


![OBJECT.png](file:///home/paul/diploma/Images/diagram/OBJECT.png)

 Obiekty mogą być wszystkimi podstawowymi trójwymiarowymi prymitywami

![expression.png](file:///home/paul/diploma/Images/diagram/expression.png)
Expression to wyrażenia, które umożliwiają podstawowe operacje matematyczne takie jak negacja, mnożenie, dzielenie, dodawania i odejmowanie a także wyrażenia z użyciem nawiasów. Kolejność występowania tych wyrażeń ma znaczeniena kolejność wykonania. Najwyższy priorytet ma nawias, następnie mnożenie i dzielenie, dodawanie i odejmowanie i ostatecznie negacja. 

#### Instalacja
Aby zainstalować dodatek na systemie Linux należy uprzednio posiadać:
- Git - w celu pobrania dodatku z platformy github
- Blender w warsji 3.1.0
- Python 3
- Lark - moduł umożliwiający parsowanie tekstu
Proces instalacji przebiega następując:
1. W terminalu należy wybrać lokację docelową dla dodatku, a następnie wpisać komendę:
``` sh
git clone https://github.com/PaulTony7/openscad_to_blender.git
```

![[Blender.png]]

![[Edit-Preferences.png]]
2. Uruchomić Blendera, a następnie przejść do Edit->Preferences->Add-Ons. Po otworzeniu menu z wyborem dodatków nacisnąć "Install", zlokalizować plik z pobranym dodatkiem i wybrać plik SCAD.py. 
![[Add-ons menu.png]]
![[Install SCAD.py.png]]
3. W celu uruchomienia dodatku należy go zlokalizować wśród listy obecnie zainstalowanych dodatków, a następnie zaznaczyć opcję "Enable". 
![[Pasted image 20220615141953.png]]

   ![[Enable add-on.png]]
#### Obsługa
![[Blender Scripting.png]]

![[OpenSCAD language in Blender.png]]

![[Scene Generated using BlenderSCAD.png]]
