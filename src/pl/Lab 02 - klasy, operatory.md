Klasy - operatory, konwersje
=============

Klasa Rational
-------------------------------------------
Zaprojektujmy klasę `Rational` służącą do operacji na ułamkach zwykłych. Zaprogramowanie takiej klasy możemy podzielić na trzy etapy:

### Ustalenie wymagań dotyczących klasy

* ma przechowywać ułamki zwykłe (całkowity licznik (*numerator*) i mianownik (*denominator*))
* ma oferować operacje takie jak dodawanie i odejmowanie
* ma wyświetlać ułamek jak zwykłą liczbę jeżeli mianownik jest równy 1, w przeciwnym przypadku w notacji „#/#”

### Ustalenie *interfejsu*

Klasa powinna zawierać metody pozwalające na:
* ustawienie licznika i mianownika ułamka
* pobranie wartości licznika i mianownika ułamka
* osobnego ustawiania licznika i mianownika ułamka
* wykonania przewidzianych operacji matematycznych
* wypisywania wartości ułamka

### Implementacja

Rozbijmy kod na plik nagłówkowy oraz źródłowy. Jeśli implementacja metody znajduje się poza definicją klasy, należy jej nazwę poprzedzić ciągiem `NazwaKlasy::`, aby wskazać kompilatorowi, że jest to metoda należąca do klasy, a nie wolna funkcja. Dodatkowo, metody które nie powodują zmian w obiekcie, na którym są wywoływane (np. gettery, funkcja drukująca) warto oznaczyć modyfikatorem `const` występującym po liście argumentów - dzięki temu będzie możliwe ich wywołanie w kontekście, do którego *obiekt* danej klasy został przekazany jako *const*. Poniżej przedstawiono przykładową implementację klasy:

`Rational.h`:

```cpp
class Rational {
public:
    Rational(int num = 0, int den = 1); // default constructor
    
    void set(int num, int den); // sets numerator and denominator
    int num() const; // gets numerator
    int den() const; // gets denominator
    void set_num(int num); // sets numerator
    void set_den(int den); // sets denominator
    Rational add(const Rational &other) const; // adds second rational number, returns result
    Rational subtract(const Rational &other) const; // subtracts second rational number, returns result
    void print() const; // prints number to console
private:
    int num_, den_;
};
```

`Rational.cpp`:

```cpp
Rational::Rational(int num, int den) : num_(num) { // inicjalizuje pole num_ wartoscia num
    if (den) {
        den_ = den;
    } else {
        den = 1;
    }
}

void Rational::set(int num, int den) {
    num_ = num;
    if (den) {
        den_ = den;
    }
}

int Rational::num() const {
    return num_;
}

int Rational::den() const {
    return den_;
}

void Rational::set_num(int num) {
    num_ = num;
}

void Rational::set_den(int den) {
    if (den) {
        den_ = den;
    }
}

Rational Rational::add(const Rational &other) const {
    return Rational(num_ * other.den_ + other.num_ * den_,
                    den_ * other.den_);
}

Rational Rational::subtract(const Rational &other) const {
    return Rational(num_ * other.den_ - other.num_ * den_,
                    den_ * other.den_);
}

void Rational::print() const {
    if (den_ == 1) {
        std::cout << num_;
    } else {
        std::cout << num_ << "/" << den_;
    }
}
```

Przetestuj działanie klasy uruchamiając poniższy kod:

```cpp
Rational quarter(1, 4);
Rational one_third(1, 3);

Rational add_result, sub_result;
add_result = one_third.add(quarter);
sub_result = one_third.subtract(quarter);

one_third.print(); cout << " + "; quarter.print(); cout << " = "; add_result.print(); cout << endl;
one_third.print(); cout << " - "; quarter.print(); cout << " = "; sub_result.print(); cout << endl;
```


Przeciążanie operatorów
-----------------------------------

Napisana w poprzednim podpunkcie klasa pozwala na operacje na ułamkach, ciągle jednak nie jest tak intuicyjna w użyciu jak typy wbudowane:
```cpp
int a = 1, b = 2;
int c = a + b;
```
Język C++ umożliwia jednak zdefiniowanie (przeciążenie) operatorów we własnych klasach, tak aby realizowały dowolną zaimplementowaną przez nas funkcjonalność. Funkcje przeciążające operatory, mogą przyjmować tyle argumentów, ile przyjmują w przypadku typów wbudowanych. Zamianie nie ulegają również priorytety operatorów, w związku z czym kolejność wykonania wyrażenia `d = a+b*c` będzie zawsze `a+(b*c)`, niezależnie od tego czy `a`, `b`, `c` i `d` to typy wbudowane, czy definiowane przez użytkownika i jaką tak naprawdę operację realizują poszczególne operatory.
Chociaż każdy operator to tylko symbol graficzny i może realizować dowolną funkcjonalność, zalecane jest aby przeciążone operatory pełniły funkcje zbliżone lub analogiczne do oryginałów, tak aby zachować czytelność kodu i intuicyjność ich użycia.

Deklaracja operatora wygląda podobnie jak deklaracja metody:

```cpp
typ_zwracany operator#(lista argumentow)
```
gdzie `#` to przeciążany operator

Przykładowe operatory dodawania i odejmowania dla klasy `Rational` mogą wyglądać następująco (analogicznie do metod `add` i `subtract`):

```cpp
Rational operator+(const Rational &rhs) const; // rhs - Right Hand Side - prawy operand
Rational operator-(const Rational &rhs) const;
```

Zwróć uwagę, że powyższe operatory przyjmują tylko jeden argument - prawy operand. Operator, tak jak metody `add` i `subtract` jest wykonywany *na obiekcie* będącym lewym operandem:

```cpp
Rational Rational::operator+(const Rational &rhs) const {
    return Rational(num_ * rhs.den_ + rhs.num_ * den_,
                    den_ * rhs.den_);
}

Rational Rational::operator-(const Rational &rhs) const {
    return Rational(num_ * rhs.den_ - rhs.num_ * den_,
                    den_ * rhs.den_);
}
```
Kod wykonujący obliczenia może mieć w tym momencie postać:
```cpp
add_result = one_third + quarter;
sub_result = one_third - quarter;
```

---
#### Zadanie do realizacji
Dodaj do klasy `Rational` powyższe operatory.
Samodzielnie zaimplementuj operator mnożenia `*`. Przetestuj jego działanie.

---

### Klasy zaprzyjaźnione, operator strumieniowy

Nie zawsze działanie operatorów jest toższsame z ich pierwotnym przeznaczeniem. Przykładowo, operatory `<<` oraz `>>`, oryginalnie służyły do przesunięć bitowych na liczbach, jednak ze względu na intuicyjność graficzną (kształ strzałki) w C++ znalazły zastosowanie w strumieniach.

Wyświetlanie wartości ułamka przy użyciu metody `print()` nie jest wygodne, nie umożliwia też np. zapisu do pliku tekstowego. Wygodniejsza byłaby forma:

```cpp
cout << one_third << " - " << quarter << " = " << sub_result << endl;
```

W przypadku operatora `<<` lewym operandem jest *strumień*, a prawym - element do dodania do strumienia. Na przykładzie operatorów `+` i `-` widzieliśmy jednak, że lewy operand jest niejako narzucony - jest to obiekt klasy, w której piszemy metodę. Nie możemy również zmodyfikować klasy strumienia - jest ona częścią systemu.

Możemy w takim wypadku napisać wolną funkcję, która przyjmie *strumień* i nasz ułamek, i zwróci zmodyfikowany strumień:

```cpp
ostream &operator<<(ostream &str, Rational &rhs){
    if (rhs.den_ == 1) {
        str << rhs.num_;
    } else {
        str << rhs.num_ << "/" << rhs.den_;
    }
    return str;
}
```

Dodaj powyższy kod do pliku `Rational.cpp`

Wolne funkcje nie mają jednak dostępu do pól prywatnych klasy, w związku z czym musimy zadeklarować przyjaźń klasy z funkcją. Wewnątrz definicji klasy `Rational` dodaj:
```cpp
friend ostream &operator<<(ostream &str, Rational &rhs);
```

---
#### Zadanie do realizacji
Dodaj do klasy `Rational` operator  `>>`, dzięki któremu będzie można pobrać od użytkownika ułamek.
Dopuść dwa możliwe formaty wejścia:
* liczba całkowita, np: `5`
* ułamek, np: `7/22`

---

Konwersje
-----------------------------------

W wielu przypadkach kompilator, jeśli nie znajdzie funkcji, która przyjmuje zestaw parametrów typu który jej przekazaliśmy, wykona *niejawną konwersję* do pasującego typu. Dzięki temu możliwe jest wykonanie kodu:

```cpp
Rational p = quarter + 1;
```

Kompilator będzie się starał wykonać działanie na zmiennej typu `Rational`, bo taki jest pierwszy operand. Zaimplementowaliśmy operację `Rational + Rational`, zatem potrzebna jest jedynie konwersja liczby całkowitej do `Rational` - którą też zaimplementowaliśmy, jako efekt uboczny konstruktora `Rational(int num, int den)`. Powyższy kod sprowadzi się zatem *niejawnie* (bez naszej wiedzy/ingerencji) do:

```cpp
Rational p = quarter + Rational(1);
```

Operacja odwrotna (`1 + quarter`) nie powiedzie się jednak, ponieważ kompilator będzie "szukał" operatora działającego na typie `int`.

Możemy również sami deklarować funkcje konwertujące *z* innego typu do naszej klasy w postaci konstruktora:

```cpp
Rational(double r); // konstruktor konwertujacy z double
```

```cpp
Rational::Rational(double r) {
    int den = 1;
    int it = 0;
    while ((r != (int)r)&&(it<9)){
        den *= 10;
        r *= 10;
        it++;
    }
    num_ = r;
    den_ = den;
}
```
oraz *na* inny typ w postaci operatora o symbolu typu, na który konwertujemy:

```cpp
operator double(); // operator konwertujacy na double
```

```cpp
Rational::operator double(){
    return (double)num_ / (double)den_;
}
```

Po dodaniu powyższych konwersji, możliwe jest nawet wykonanie poniższego kodu, mimo że nie zdefiniowaliśmy operatora `/` dla klasy `Rational`.

```cpp
Rational p = one_third / quarter;
```
Dlaczego powyższy kod działa? Czy wynik jest dokładnie taki jak powinien być?

[//]: # (Pominięte: explicit)

Zadania
------

#### 1. Klasa `Time`

Zaprojektuj i zaimplementuj klasę pozwalającą na przechowywanie czasu i podstawowe operacje (dodawanie i odejmowanie, mnożenie przez skalar).

Klasa powinna umożliwiać wyświetlenie wartości w konsoli czasu w formacie czytelnym dla użytkownika, a także pobieranie czasu od użytkownika oraz konwersję z/na liczbę całkowitą sekund.

Przykładowe użycie:

```cpp
Time t1(200);
cout << t1 << endl; // wyswietli 03m:20s
Time t2;
cin >> t2; // uzytkownik wprowadza 10h:12m:01s

Time t3 = t2 - t1; // 10h:8m:41s
int t3s = t3; // 36521
```

#### 2. Program *SuperFaktura*

Napisz program umożliwiający generowanie faktur w konsoli. Rozbij funkcjonalność na klasy:

`Invoice` - klasa opisująca fakturę, posiadająca następujące atrybuty:
* NIP wystawcy/odbiorcy
* zbiór pozycji na fakturze

Faktura w najprostszej wersji powinna mieć możliwość dodawania elementów i "wydruku" całości informacji.

`Item` - klasa opisująca pojedynczą pozycję na fakturze, z poniższymi atrybutami:
* nazwa
* cena jednostkowa netto
* typ stawki VAT ("A" – 23%, "B" – 8%, "C" – 0%)
* sprzedana ilość

Przykładowa faktura po "wydruku" może wyglądać następująco:
```
------------------FAKTURA VAT------------------
===============================================
Sprzedawca: 7770003699      Nabywca: 0123456789

                  c.j. VAT   il.  netto  brutto
1. Sruba M3     | 0.37  A | 100 | 37.00 | 45.51
2. Wiertlo 2 mm | 2.54  B |   2 |  5.08 |  5.49

------------------------------------ RAZEM ----
                                  42.08 | 51.00
```

Zastanów się jaką funkcjonalność i jaki interfejs musi mieć każda z klas. Drukowanie zrealizuj przez operator strumieniowy, zarówno w klasie `Item` jak i `Invoice`.

Ostatecznie klasy powinny umożliwić wykonanie kodu podobnego do poniższego:

```cpp
Invoice inv(7770003699, 0123456789);
inv.add_item(Item("Sruba M3", 0.37, 'A', 100));
inv.add_item(Item("Wiertlo 2 m", 2.54, 'B', 2));
cout << inv << endl;
```

**Bonus:**

Zaimplementuj operator dodawania faktur (sprawdź zgodność NIP-ów, pozycje występujące na obu fakturach złącz w jedną). Zastanów się jak możesz przechowywać pozycje wewnątrz faktury, aby łatwo znaleźć dopasowania.

***
Autorzy: *Jakub Tomczyński*, *Michał Fularz*, *Rafał Kabaciński*, *Piotr Kaczmarek*, *Michał Nowicki*, *Jan Wietrzykowski*
