# Analiza literatury

#### Earley Parser
Earley Parser to algorytm, który pozwala na parsowanie tekstu należącego do pewnego języka bezkontekstowego. Czas wykonywania w generalnych przypadkach to  O(n^3), a w przypadkach jednoznacznej gramatyki O(n^2). Dla każdego deterministycznej bezkontekstowej gramatyki czas wykonania jest liniowy [[An Efficient context-free parsing algorithm|1]]. 
Algorytm iniciuje sekwencję zbiorów, czasem nazywanych zbiorami Earleya. Dla danych parametrów x1, x2,...xn parser buduje n+1 zbiorów: zbiór początkowy S0 i jeden Si dla każdego parametru xi. Elementy takiego zbioru są często nazywane przedmiotami Earleya i zawierają trzy części: regułę gramatyczną, pozycję po prawej stronie równania, która wskazuje jaka część reguły została już przeanalizowana i wskaźnik na poprzedni, lub aktualny zbiór. Typowy zbiór zapisany jest następująco:
[A -> a * b, j]
gdzie pozycja po prawej stronie reguły jest zaznaczona za pomocą *, a j to wskaźnik na zbiór Sj.
Zbiory Earleya analizowane są w trzech krokach:
- Skaner - jeśli [ A → ... • a...,j ] znajduje się w Si i a = xi+1 dodaj [A → ...a • ...,j ] do Si+1.
- Predyktor - jeśli [A → ... • B...,j ] jest w Si dodaj [B → •α, i] do Si dla każdej zasady B → α.
- Kompleter - jeśli [A → ... •, j ] jest w Si dodaj [B → ...A • ...,k] do Si dla każdego przedmiotu [B → ... • A...,k] w Sj
[[Practical Earlay Parsing|5]]
![[Earley Parser dla E -> E + E n.png]]
#### Text to 3D
Transformacja tekstu na scenę trójwymiarową to idea, która była już niejednokrotnie zgłębiana. Poza generacją scen programistycznie podejmowane są próby konwersji języka naturalnego na scenę trójwymiarową [[Text to 3D|2]]. Najnowsze badania są również realizowane przez Google i OpenAI do generowania obrazów dwuwymiarowych przez zaawansowane algorytmy sztucznej inteligencji. Projekty takie jak Imagen [[Imagen|3]] i DALL-E 2 [[DALL-E 2|4]] wyglądają bardzo obiecująco i dają niesamowite efekty w dziedzinie grafiki komputerowej.
#### Parametric modeling
Wraz z wprowadzeniem rachunków różniczkowych w 17-tym wieku pozwoliło na opisywanie i wyjaśnianie zaturanlych zjawisk za pomocą abstrakcji matematycznej. Wiele z tych fizyczynych fenomenów można reprodukować za pomocą kodu i wzorów matematycznych. Z pomocą paremetrycznego modelowania możliwe stało się odwzwierciedlenie naturalnie występujących zjawisk w architekturze budowlanej. Przez lata parametryczne podejście do architektury zyskiwało coraz większe znaczenie w tej dziedzinie i w obecnych czasach architekci polegają w pierwszej kolejności na programach pomagających im generować trójwymiarowe modele za pomocą parametrycznego modelowania. [[Parametric modeling for advanced architecture|6]]
Proces projektowania, rozwijania i produkcji samochodów został bardzo mocno wsparty przez technologie komputerowe już od lat 80tych. Filozofia CAD - Computer Aided Design (ang. komputerowo wsparte projektowanie) i CAM - Computer Aided Manufacutring (ang. komputerowo wsprarta produkcja)  stały się integralną częścią rynku motoryzacyjnego. Technologie te pozwoliły na skrócenie czasu cyklu innowacyjnego do 6-8 lat.[[Use of 3D Parametric Models in the Automotive Component Design Process|7]]
#### Druk 3D
Wraz ze spadkiem ceny drukarek 3D technologia druku trójwymiarowego stała się bardziej dostępna dla przeciętnego konsumenta. Ogólnodostępność tej technologii sprawiła, że zaczęła ona znajdować zastosowanie w większej ilości dziedzin. Możliwe stało się sparametryzowanie drukowanego budulcu pod projekty architekturalne. Jednym z przykładów praktycznego zastosowania jest trójwymiarowe drukowanie struktur betonowych. Ze względu na pewne nieznane czynniki takie jak ilość warstw, która gwarantuje sukcesywność, jak i różne inne czynniki powstają narzędzia, które pozwalają na sparametryzowanie tych zmiennych wartości w celu przyspieszenia procesu rozwoju tych technologii. 
![[3Dprint.jpg]]

## Alternatywne rozwiązania
Idea generowania scen trójwymiarowych za pomocą kodu nie jest nowa i była już wielokrotnie implementowana na różne sposoby. Poniżej zaprezentowane są i przeanalizowane przykłady takich rozwiązań 

#### BlenderSCAD
BlenderSCAD to dodatek do Blendera, który jest zamieszczony na publicznym repozytorium na githubie na bazie licencji GPL-3.0. Rozszerza on możliwości Blendera o częściowy język OpenSCAD. Wspieranych jest jedenaście różnych obiektów podstawowych (w tym sześć trójwymiarowych, a pięć dwuwymiarowych), sześć operacji transformujących, operacje booleanowe, cztery operacje deformujące i możliwość export i importu sceny do dwóch formatów. 
Dodatek ten jest efektem pracy jednej osoby i mimo bycia udostępnionym do modyfikacji szerszej społeczności straciło wsparcie przez oryginalnego autora w 2015 roku i nie zyskało szerszej popularności. Ostatnia wspierana wersja Blendera przez ten dodatek to 2.79, co czyni dodatek nie kompatybilny z nowszymi wersjami, gdyż od wersji 2.8 Blender otrzymał duże zmiany w API, czyniąc kompatybilność wsteczną niemożliwą. Z powodu stale rozwijającej się technologii kompatybilność z najnowszymi wersjami oprogramowania ma duże znaczenie dla jakości generowanych modeli i spójności z narzucanymi standardami.
#### OpenSCAD
OpenSCAD jest otwartym oprogramowaniem, które umożliwia korzystanie z funkcyjnego języka OpenSCAD w celu generowania scen trójwymiarowych. Program ten jest w stałym rozwoju i wciąż otrzymuje aktualizacje. Mimo kompletności języka i pełności implementacji funkcji przez niego zdefiniowanego nie jest on standardem w branży trójwymiarowej. W celach edukacyjnych lepiej jest skupić się na nauczeniu możliwie jak najmniejszej liczby aplikacji.