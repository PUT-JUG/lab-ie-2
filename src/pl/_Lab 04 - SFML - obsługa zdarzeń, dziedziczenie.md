SFML - obsługa zdarzeń, dziedziczenie
=============

Używanie bibliotek zewnętrznych
-------------------------------------------
Do tej pory na zajęciach korzystaliśmy z bibliotek standardowych dostarczanych wraz z zestawem narzędzi deweloperskich (SDK) dla danego systemu. Wykorzystanie biblioteki wymagało jedynie załączenia odpowiedniego pliku nagłówkowego w kodzie programu. W związku z faktem, że były to biblioteki standardowe, ich lokalizacja była znana dla kompilatora.

Dodanie zewnętrznej biblioteki wymaga modyfikacji ustawień projektu poprzez wskazanie gdzie dana biblioteka jest umieszczona. Ponadto, załączana przez nas biblioteka SFML będzie dostarczona w postaci skompilowanej. Oznacza to, że plik nagłówkowy będzie zawierał jedynie deklaracje funkcji, a ich implementacja dostarczona jest w postaci plików zawierających skompilowany kod, które należy wskazać *linkerowi* w celu poprawnego zbudowania projektu.

### Tworzenie projektu wykorzystującego SFML i OpenGL

Wykorzystamy środowisko Qt Creator, skonfigurowane z kompilatorem MSVC z pakietu Visual C++ 2017 dla architektury 64-bit.

Pobierz aktualną wersję biblioteki SFML ze strony jej twórców (https://www.sfml-dev.org/download/sfml/2.5.1/). Wybierz wersję odpowiednią dla używanego systemu, kompilatora i architektury (*Visual C++ 15 (2017) - 64-bit*). Rozpakuj ją w wybrane miejsce na dysku, instrukcja zakłada lokalizację katalogu z biblioteką bezpośrednio na dysku `C:`.

Stwórz projekt aplikacji zgodnie z instrukcją [Instalacja Qt Creator](Instalacja%20Qt%20Creator.html). Umieść w projekcie plik `main.cpp` z zawartością pobraną z odnośnika [hello_sfml.cpp](../resources/hello_sfml.cpp).

Spróbuj skompilować projekt. **Kompilator** zwróci błąd o braku pliku nagłówkowego `SFML/Window.h` - nie ma go w ścieżkach systemowych, które przeszukuje. Dodaj brakującą ścieżkę do ustawień projektu modyfikując plik `.pro` opisujący projekt:

```
INCLUDEPATH += "C:/SFML-2.5.1/include"
```

Wymuś ponowne przetworzenie pliku `.pro` klikając prawym przyciskiem na nazwę projektu w drzewie i wybierając *Run qmake*.

Ponownie spróbuj skompilować projekt. Tym razem błędy typu *unresolved external symbol* zwróci **linker** - program "składający" ostateczny plik wykonywalny z wcześniej skompilowanych fragmentów. Oznacza to, że funkcje, których użyto w programie zostały znalezione w plikach nagłówkowych, ale nie udało się znaleźć ich skompilowanej implementacji - zawarta jest ona w plikach `*.lib` dostarczonych z biblioteką. Dodaj do pliku `*.pro` dodatkowe parametry linkera zawierające ścieżkę, pod którą można je znaleźć, a także listę plików, które mają zostać zlinkowane:

```
LIBS += -L"C:/SFML-2.5.1/lib"
LIBS += -lsfml-audio -lsfml-graphics -lsfml-network -lsfml-system -lsfml-window
```

Ponieważ wykorzystujemy również systemowe biblioteki OpenGL i OpenGL Utility Libraries, dodaj także:

```
LIBS += -lOpenGL32 -lglu32
```

Ponownie uruchom *qmake*, a następnie skompiluj projekt. Kompilacja powinna przebiec bez błędów, jednak nadal nie będzie możliwe uruchomienie programu.

Wynika to z faktu, że biblioteki, które dołączyliśmy do projektu nie są wkompilowane w plik wykonywalny - są łączone dynamicznie (ang. *DLL - Dynamic-Link Library*) i ładowane z zewnętrznych plików w trakcie uruchamiania. Mogą być dzięki temu współdzielone pomiędzy wiele programów. Pliki DLL można umieścić w katalogach systemowych lub w katalogu, gdzie znajduje się plik wykonywalny programu.

Skopiuj pliki `.dll` z katalogu `bin` biblioteki SFML do katalogu, w którym znajduje się plik wykonywalny  (`exe`) utworzonej przez Ciebie aplikacji. W przypadku środowiska Qt Creator, katalog kompilacji domyślnie znajduje się obok katalogu projektu i ma nazwę *build-<nazwa_projektu>-<nazwa_kitu>-<nazwa_wydania>*, np. *build-sfml_hello-Desktop_Qt_5_10_1_MSVC2017_64bit-Debug*. Następnie uruchom projekt. Poprawnie działający program powinien wyświetlić następujące okienko:

![](../images/hello_sfml.png)

Biblioteka SFML
----------

SFML jest wieloplatformową biblioteką ułatwiających tworzenie programów wykorzystujących grafikę dwuwymiarową, np. prostych gier. Zawiera moduły pozwalające na m.in. obsługę grafiki, wejścia klawiatury/myszy, dźwięku i sieci. Ponieważ





***
Autorzy: *Jakub Tomczyński*, *Michał Fularz*, *Piotr Kaczmarek*, *Michał Nowicki*, *Jan Wietrzykowski*
