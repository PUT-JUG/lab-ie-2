Polimorfizm, tekstury
=====================

Wskaźniki
---------
Istnieją dwa rodzaje pamięci: stos oraz sterta. Do tej pory staraliśmy się unikać alokacji pamięci na stercie.

Zmienne i obiekty alokowane na stosie są usuwane w momencie wyjścia poza zakres (*scope*), w którym zostały zaalokowane. W większości przypadków jest to działanie pożądane. Istnieją jednak przypadki, w których wskazane jest oddzielenie czasu życia obiektu od zakresu w którym został on utworzony. W tym celu używamy alokacji na stercie.

W celu alokacji zmiennych na stercie klasycznie używano operatora `new`. Tak utworzoną zmienną należało następnie usunąć za pomocą operatora `delete`. Jako, że o operacji dealokacji łatwo zapomnieć, wiele programów do dziś przejawia problem nazywany **wyciekiem pamięci**.

---
#### Zadanie do realizacji
Wykonaj poniższy kod obserwując zużycie pamięci w *Menedżerze zadań* (Ctrl + Alt + Delete):
```cpp
for (int i = 0; i < 67108864; i++) {
  int *number = new int;
}
std::getchar();
```

---

W celu unikania wycieków pamięci w C++11 wprowadzono nowe typy, w tym `std::unique_ptr` oraz `std::shared_ptr`. W tej instrukcji pominiemy `std::shared_ptr`, ponieważ służy on do przechowywania obiektów, które mają wielu właścicieli (na przykład znajdują się jednocześnie w więcej niż jednym wektorze). Dla `std::unique_ptr` nie może być kopiowany - obiekt na który wskazuje ma tylko jednego właściciela.

`std::unique_ptr` to stosunkowo nieskomplikowana klasa, która, w sporym uproszczeniu, w konstruktorze wykonuje alokację obiektu przy pomocy `new`, a w destruktorze niszczy go za pomocą `delete`. Zastępując wykorzystywanie zwykłych wskaźników w stylu języka C użyciem `std::unique_ptr` można łatwo zapobiec wyciekom pamięci. Zmienna typu `std::unique_ptr` wywoła `delete` na przechowywanym wskaźniku kiedy wyjdzie z zakresu, w którym została zadeklarowana.

---
#### Zadanie do realizacji
Zmień poprzedni kod tak, aby zamiast operatora `new` wykorzystywał typ `std::unique_ptr`. Do stworzenia `std::unique_ptr` wykorzystaj funkcję [`std::make_unique`](https://en.cppreference.com/w/cpp/memory/unique_ptr/make_unique). Obserwuj zużycie pamięci.

**Wskazówka:** jako, że do funkcji `std::make_unique` przekazujemy typ zmiennej, którą chcemy utworzyć na stercie możemy **opcjonalnie** użyć słowa kluczowego `auto` w celu ominięcia deklarowania obiektu o długiej nazwie typu: `std::unique_ptr<int>`. Nie spowoduje to utraty czytelności kodu.

---

Po przeczytaniu powyższego opisu i wykonaniu zadania można odnieść wrażenie, że alokacja na stosie oraz alokacja na stercie z użyciem `std::unique_ptr` są funkcjonalnie identyczne. W obu przypadkach obiekt niszczony jest po wyjściu z zakresu w którym został zadeklarowany. Po co więc używać `std::unique_ptr`?

W C++11, obok typu `std::unique_ptr` wprowadzono również *move semantics* - możliwość "przeniesienia" własności obiektu do innego zakresu (*scope*) lub kontenera. O ile w przypadku obiektów zadeklarowanych na stosie owo przeniesienie w wielu przypadkach spowoduje konieczność wykonania kopii przynajmniej części pól przenoszonego obiektu, to w przypadku `std::unique_ptr` przeniesienie nie spowoduje kopiowania obiektu na stercie.

---
#### Zadanie do realizacji
Spróbuj skompilować poniższy kod:
```cpp
std::vector<std::unique_ptr<int>> some_pointers;
for (int i = 0; i < 67108864; i++) {
  auto number = std::make_unique<int>();
  some_pointers.emplace_back(number);
}
```

Zastanów się dlaczego tak napisany program nie działa.

Pamiętając, że obiekt na który wskazuje `std::unique_ptr` może mieć tylko jednego właściciela przekaż "prawo własności" wektorowi wywołując na zmiennej `number` funkcję [`std::move`](https://en.cppreference.com/w/cpp/utility/move).

---

Polimorfizm
-----------
Użycie wskaźników na obiekty ze sterty zamiast obiektów na stosie pozwala na łatwe zinterpretowanie wskaźnika na obiekt klasy pochodnej jako wskaźnika na obiekt klasy bazowej.

---
#### Zadanie do realizacji
Wykorzystując przykładowe klasy z poprzedniej instrukcji spróbuj skompilować poniższy kod:
```cpp
std::unique_ptr<Vehicle> skoda_superb_as_vehicle = std::make_unique<Car>(
    "Skoda Superb", PropulsionType::Gasoline, 200, true);

std::cout << "Name: " << skoda_superb_as_vehicle->name() << std::endl;
std::cout << "Has ABS: " << skoda_superb_as_vehicle->has_abs() << std::endl;
```

Wiedząc, że traktujemy nasz samochód jako obiekt klasy `Vehicle` oraz analizując metody które są w tej klasie zaimplementowane zastanów się dlaczego tak napisany program nie działa. Usuń linijkę powodującą błąd kompilacji.

Zwróć uwagę, na różnicę w odwoływaniu się do pól obiektu (`->` zamiast `.`). Z czego ona wynika?

---



***
Autorzy: *Dominik Pieczyński*, *Jakub Tomczyński*
