# Opis implementacji
#### Analiza kodu
``` ebnf
start: instruction+

instruction: action ";"
| action operator+ ";"
| IDENTIFIER "=" (expression) ";" -> assign
| "{" instruction+ "}" operator+ ";" -> block

action: OBJECT "(" (INTEGER | FLOAT | vector | IDENTIFIER) ")"

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
  
OBJECT: "cube"|"square"|"circle"|"sphere"|"cylinder"

TRANSFORM: "translate"|"rotate"|"scale"

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
Powyższy kod przedstawia definicję gramatyki języka bazowanego na języku OpenSCAD. Definicja gramatyki została wykonana w EBNF (Extended Backus-Naur form)
![Start|300](file:///home/paul/diploma/Images/diagram/start.png)

![instruction.png](file:///home/paul/diploma/Images/diagram/instruction.png)

![operator.png](file:///home/paul/diploma/Images/diagram/operator.png)

![expression.png](file:///home/paul/diploma/Images/diagram/expression.png)

![vector.png](file:///home/paul/diploma/Images/diagram/vector.png)

![OBJECT.png](file:///home/paul/diploma/Images/diagram/OBJECT.png)
 
![TRANSFORM.png](file:///home/paul/diploma/Images/diagram/TRANSFORM.png)

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

![[Pasted image 20220614155255.png]]

![[Pasted image 20220614155203.png]]
2. Uruchomić Blendera, a następnie przejść do Edit->Preferences->Add-Ons. Po otworzeniu menu z wyborem dodatków nacisnąć "Install", zlokalizować plik z pobranym dodatkiem i wybrać plik SCAD.py. 
![[Pasted image 20220614155927.png]]
![[Pasted image 20220614160009.png]]
3. W celu uruchomienia dodatku należy go zlokalizować wśród listy obecnie zainstalowanych dodatków, a następnie zaznaczyć opcję "Enable". 
   ![[Pasted image 20220614160030.png]]
   ![[Pasted image 20220614160107.png]]
#### Obsługa
![[Pasted image 20220614160136.png]]

![[Pasted image 20220614160805.png]]

![[Pasted image 20220614160828.png]]
