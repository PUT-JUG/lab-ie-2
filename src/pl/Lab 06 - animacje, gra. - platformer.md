Animacje, gra - platformer
=============

Animacje
-------------------------------------------
Animacje w grach zbudowanych na sprite'ach opierają się na zestawie następujących po sobie klatek animacji, przełączanych w odpowiednich momentach.

Często kolejne klatki animacji umieszczone są w jednej teksturze, a wybór danej klatki odbywa się poprzez wskazanie odpowiedniego fragmentu tekstury (przypomnienie: metoda `setTextureRect(sf::Rect)`. Dzięki temu cała animacja może być przechowywana w pamięci układu graficznego, a przejście do kolejnej klatki animacji nie jest kosztowne obliczeniowo.

---
#### Zadanie do realizacji

Napisz klasę `AnimatedSprite` dziedziczącą po `sf::Sprite`.

Klasa powinna mieć interfejs, który pozwoli na wskazanie fragmentów tekstury będących kolejnymi klatkami animacji:

```cpp
AnimatedSprite hero;

// add texture, set parameters

hero.add_animation_frame(sf::Rect(0, 0, 32, 32));
hero.add_animation_frame(sf::Rect(32, 0, 32, 32));
hero.add_animation_frame(sf::Rect(64, 0, 32, 32));
```

 Analogicznie do poprzednich klas, ma ona mieć metodę `step`, dzięki której będzie możliwe informowanie obiektu o czasie jaki upłynął. Klasa na podstawie tego czasu oraz wewnętrznego parametru opisującego liczbę klatek na sekundę, powinna decydować, czy ma nastąpić przełączenie na kolejną klatkę animacji.
 
 Stwórz na scenie obiekt klasy `AnimatedSprite`, wykorzystaj teksturę postaci z [załączonego zestawu tekstur](../resources/sprites.zip).

---

Grawitacja
-------------------------------------------

Grawitacja to stałe przyspieszenie działające w pionowym kierunku. Tak samo, jak w każdym *kwancie czasu* bieżąca *prędkość* wpływa na położenie obiektu, tak bieżące *przyspieszenie* wpływa na *prędkość*.

---
#### Zadanie do realizacji

Dodaj do klasy `AnimatedSprite` informację o prędkości obiektu, analogicznie do klasy `CustomRectangleShape`.
Dodaj kolejną pochodną położenia - przyspieszenie i dodaj odpowiednie oblicznenia w metodzie `step`. Ustaw pionową składową przyspieszenia tak, aby obiekt "spadał". Dodaj odpowiednie parametry i warunki opisujące prędkość graniczną.

---

Zadanie
-----------
#### 1. Gra - "platformer"

Bazując na kodzie służącym do wykrywania kolizji w zadaniu z poprzednich zajęć, napisz prosty silnik gry platformowej:

* dodaj do sceny kilka obiektów - platform z odpowiednią teksturą
* dodaj postać - `AnimatedSprite`, którą będziesz mógł poruszać klawiszami kursora w lewo i w prawo, ustal dla niej grawitację
* dodaj kod wykrywający kolizje pomiędzy postacią a którąkolwiek z platform, tak aby postać mogła stać na platformach

***
Autorzy: *Jakub Tomczyński*
