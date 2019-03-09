OpenGL
=============

OpenGL i GLU w SFML
-------------------------------------------
OpenGL jest otwartym niskopoziomowym API pozwalającym na generowanie trójwymiarowej grafiki. Ponieważ biblioteka SFML wykorzystuje wewnętrznie OpenGL do rysowania grafiki 2D, możemy ją wykorzystać jako bibliotekę pomocniczą ułatwiającą np. utworzenie okna i obsługę zdarzeń wejścia.

OpenGL dostarcza m.in. funkcje rysujące pojedyncze wielokąty lub serie wielokątów oraz ich przekształcenia. Nieco bardziej wysokopoziomowe funkcje są częścią biblioteki GLU (GL Utilility Library).

Ponieważ będziemy wykorzystywać bezpośrednio funkcje OpenGL oraz GLU, wymagane jest dołączenie ich do listy bibliotek dla przekazanych w parametrach linkerowi. W przypadku środowiska Qt Creator, dodajemy do pliku `*.pro` poniższą linię:

```
LIBS += -lOpenGL32 -lglu32
```

### Pierwszy program OpenGL + SFML




***
Autorzy: *Jakub Tomczyński*
