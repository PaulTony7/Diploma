# Wstęp



# Cel pracy
Celem pracy jest stworzenie zintegorwanego dodatku w środowisku Blender, pozwalającego na generowanie scen 3D poprzez pisanie kodu o stylistyce programistycznej, z pominięciem etapu projektowania w domyślenie przeznaczonym do tego GUI. Struktura kodu programistycznego zaprojektowana w pracy jest wzorowana na języku skryptowym wykorzystywanym w środowisku OpenSCAD

# Zakres pracy
Zakresem pracy jest 
- Analiza literatury
- Przegląd konkurencyjnych rozwiązań
- Opracowanie sposobu parsowania języka wzorowanego na języku skryptowym OpenSCAD
- Integracja parsera z API wystawionym przez środowisko Blender jako dodatku
- Testy dodatku

# Układ pracy
Układ pracy prezentuje się następująco. W pierwszym rozdziale jest wstępem teoretyczny z wiedzą konieczną do zaimplementowania dodatku. W rozdziale tym znajduje się analiza literatury, dzięki któremu możliwe uzasaniony będzie wybór odpowiednich narzędzi i bibliotek. W rozdziale tym znajduje się też przegląd alternatywnych rozwiązań, dzięki którym również można wygenerować scenę trójwymiarową za pomocą języka skryptowego i przy użyciu środowiska graficznego. 

#### Drugi rozdział - projekt dodatku?
#### Trzeci rozdział - implementacja?
#### Czwarty rozdział - Testy?
#### Piąty rozdział - Podsumowanie?

# Opis narzędzi:
## Blender
Pierwsza wersja Blendera została pierwotnie wydana w styczniu 1995 roku przez Holenderskie studio NeoGeo. Ostatnia stabilna wersja 3.1 została opublikowana 9 marca 2022 roku. Blender jest stale wspierane i rozwijane przez Blender Foundation. Na chwilę obecną zostały zapowiedziane 3 daty publikacji kolejnych wersji: 3.2 w Czerwcu, 3.3 z przedłużonym czasem wsparcia we wrześniu i 3.4 zapowiedziana na grudzień.
Blender to wolne i otwarte oprogramowanie operujące w trójwymiarowej grafice komputerowej służące do pracy nad animacjami, specjalnymi efektami, trójwymiarowymi scenami, trójwymiarowymi modelami do druku.
Blender jako program służący do pracy z środowiskiem trójwymiarowym został zaprojektowany w C/C++, co dzięki niskopoziomowej naturze tej grupy języków pozwala na zoptymalizowanie czasowe operacji matematycznych. Program został zintegrowany z Pythonem, w którym zostały wyodrębnione operacje wykorzystywane przez program. Dzięki takiej strukturze możliwe jest pisanie własnych modyfikacji dla funkcjonalności Blendera.
## Blender API
Część Blendera napisana w pythonie i wspierająca dodawanie własnych modyfikacji została nazwana Blender API. Pozwala to na wykorzystywanie podstawowych operacji programu na cztery główne sposoby:
- Utworzenie dodatku
- Uruchomienie skryptu wraz z programem
- Korzystanie z konsoli wewnątrz programu
- Uruchamianie skryptów wewnątrz programu
Z Api Blendera korzystamy importując moduł bpy. Moduł bpy dzieli się na 9 podmodułów. Sa to:
 - context - dostęp do obszaru Blendera aktualnie użytkowanego
 - data - dostęp do wszystkich danych
 - msgbus - podmoduł wykorzystywany do otrzymywaniu powiadomień o wszystkich zmienionych właściwościach przez API
 - ops - dostęp do operatorów, funkcję tutaj są często zmapowane do interfejsu użytkownika
 - types - typy danych wykorzystywanych wewnątrz Blendera
 - utils - dostęp do funkcji specyficznych dla Blendera, ale nie związanych z wewnętrznymi danymi Blendera
 - path - podmoduł wspierający pracę na ścieżkach, Blenderowa implementacja os.path
 - app - podmoduł zawierający dane niezmienne w trakcie działania programu takie jak wersja programu, ścieżka do programu, czy program został uruchomiony w tle itd.
 - props - definiuje właściwości rozszerzające wewnętrzną strukturę danych Blendera
## OpenSCAD język

język skryptowy OpenSCAD to funkcyjny język programowania, służący do generowania dwu i trójwymiarowych modeli wykorzystywany w programie OpenSCAD. Struktura tego języka to lista wyrażeń. 
Język składa się z komponentów:
- objektów - podstawowy budulec dla scen trójwymiarowych. Tworzą go dwu i trójwymiarowe prymitywy
- akcji - wyrażenia te zawierają w sobie tworzenie nowych modeli jak i przypisywanie wartości do zmiennych
- operatorów - pozwalają na modyfikowanie właściwości objektów takich jak pozycja, rotacja, skala, kolor. nawiasy klamrowe '{}' pozwalają operatorom na działanie na więcej niż jednej akcji. 
Przykładowy fragment kodu zostanie zaprezentowany w poniższym kodzie:
``` scad
sphere(3);
y = 2;
x = y * 3;
translate([10,0,0]) color("blue") cube([y, x, y]);
```
W powyższym kodzie utworzona zostaje sfera o promieniu 3 jednostek, następnie przypisana zostaje wartość 2 do zmiennej y i do zmiennej x trzykrotność y, czyli 6. Ostatnie wyrażenie generuje niebieski prostopadłościan o wymiarach bazujących na wcześniej utworzonych zmiennych, a następnie przemieszczony jest za pomocą operatora translate o 10 jednostek na osi x. Warto zwrócić uwagę, że operacje mogą być wielokrotnie wykonane na tym samym objekcie.
powyższy kod ma następującą strukturę:
```scad
action();
action;
action;
operation() operation() action();
```
Operacje poprzedzają akcje, na których są wykonywane. 
Trójwymiarowość kodu skryptowego OpenSCAD uzasadnia wsparcie wektorów wielowymiarowych. Wektory definiowane są za pomocą nawiasów prostokątnych '[]'', każdy z wymiarów przedzielony jest przecinkiem ','.
