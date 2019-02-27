Wstęp do klas
=============

Proste struktury i funkcje w stylu języka C
-------------------------------------------
Do tej pory tworzone na zajęciach struktury zawierały jedynie pola będące zmiennymi różnych typów. Jako przykład można potraktować strukturę `Student`:
```cpp
struct Student {
    std::string name;
    std::string surname;
    std::vector<float> grades;
};
```

Zakładając, że chcemy wyliczyć średnią ocen należy napisać odpowiednią funkcję, które wykona tę operację dla danego obiektu struktury `Student`:
```cpp
float calculate_grade(const Student &student) {
    float sum = std::accumulate(student.grades.begin(), student.grades.end(), 0.0f);
    return sum / student.grades.size();
}
```

Takie podejście niesie ze sobą pewne konsekwencje: szczególnie w większych projektach powstaje dużo wolnych (nie należących do żadnej klasy), globalnie dostępnych funkcji o różnych nazwach, które nie są hierarchicznie ułożone.

Proste struktury w stylu języka C++
-----------------------------------
### Podstawowe informacje
Funkcje powiązane z daną strukturą można zadeklarować wewnątrz jej deklaracji. Taka funkcja jest wtedy nazywaną **metodą** i ma dostęp do wszystkich aktualnych wartości przechowywanych w obiekcie danej struktury.

Zmodyfikowana deklaracja struktury `Student` może wyglądać następująco:
```cpp
struct Student {
    std::string name;
    std::string surname;
    std::vector<float> grades;

    float calculate_grade() {
        float sum = std::accumulate(grades.begin(), grades.end(), 0.0f);
        return sum / grades.size();
    }
};
```

Tym razem metoda `calculate_grade` jest zdefiniowana wewnątrz struktury `Student` i nie przyjmuje żadnych parametrów. Ma ona jednak dostęp do wartości wszystkich pól obiektu `Student`.

Odwoływanie się do metod zadeklarowanych w strukturach odbywa się na analogicznej zasadzie do odwoływania się do pól obiektu:
```cpp
Student student{"Some", "Student", {2, 3, 4, 5, 3}}; // This creates object of Student type
std::cout << student.calculate_grade() << std::endl; // This calls calculate_grade function and prints the result
```

---
#### Zadanie do realizacji
Dodaj metodę `print` wewnątrz struktury `Student`, która wydrukuje imię i nazwisko studenta oraz wszystkie jego oceny:
```
Jan Kowalski: 3.0 4.5 5.0 3.5
```

---

### Weryfikacja poprawności wprowadzanych danych
Napisany program w chwili obecnej nie dokonuje żadnego sprawdzania wprowadzanych danych. Studentowi można przypisywać oceny, które będą dowolnymi liczbami.

Przykładowa metoda (wewnątrz struktury `Student`), która umożliwia dodanie nowej oceny wraz z weryfikacją jej poprawności może wyglądać następująco:
```cpp
bool add_grade(float grade) {
    if (grade >= 2.0 && grade <= 5.0) {
        // The grade is valid; let's add it and return true
        grades.push_back(grade);
        return true;
    }
    // The grade is invalid; let's return false
    return false;
}
```

Powyższe rozwiązanie nie rozwiązuje jednak wszystkich problemów. Do obiektu struktury `Student` nadal można dodać ocenę pomijając wywołanie `add_grade`:
```cpp
Student student;
student.grades.push_back(8.0);
```

Dodatkowo zmienna typu `Student` może być zainicjalizowana ocenami z błędnego przedziału:
```cpp
Student student{"Jan", "Kowalski", {5, 10, 15}};
```

Korzystanie z tak przygotowanego interfejsu wymaga dużej samodyscypliny oraz przygotowania dokumentacji informującej osobę mającą używać takiego kodu o konieczności dodawania ocen tylko z użyciem metody `add_grade`.

Klasy jako alternatywa struktur
--------------------------------
Problemy opisane powyżej mogą być rozwiązane przy użyciu klas. Koncepcyjnie klasy przypominają struktury: również posiadają pola i metody. Pozwalają jednak osobie projektującej klasę ograniczyć sposoby, w jaki możliwy będzie dostęp do nich &bdquo;z zewnątrz&ldquo;.

---
#### Zadanie do realizacji

Zmień deklarację struktury `struct Student` na `class Student`. Spróbuj skompilować kod odwołujący się do pól lub metod klasy `Student`.

---

Istnieją trzy modyfikatory dostępu do pól i metod struktury lub klasy: *public*, *protected* i *private*. Początkowo pominiemy wykorzystanie modyfikatora *protected*.
* Modyfikator *public* oznacza, że do pól i metod oznaczonych tym modyfikatorem można odwoływać się z kodu znajdującego się poza strukturą lub klasą. **Wszystkie pola i metody struktur są domyślnie publiczne**.
* Modyfikator *private* oznacza, że do pól i metod oznaczonych tym modyfikatorem można odwoływać się tylko z kodu znajdującego się wewnątrz metod struktury lub klasy. **Wszystkie pola i metody klas są domyślnie prywatne**.

Jedyną różnicą między klasami i strukturami w języku C++ jest domyślny modyfikator dostępu. W praktyce deklaracja następującej struktury:
```cpp
struct Student {
    std::string name;
    std::string surname;
}
```
jest równoznaczna następującej deklaracji klasy:
```cpp
class Student {
public:
    std::string name;
    std::string surname;
}
```

Modyfikator obowiązuje dla wszystkich pól i metod zadeklarowanych pod nim, aż do pojawienia się kolejnego modyfikatora.

Dodatkową korzyścią wynikającą z chronienia pól i umożliwienia do nich dostępu jedynie przez metody publiczne jest fakt, że osoba korzystająca z klasy nie musi przejmować się tym, w jaki sposób przechowywane są informacje wewnątrz klasy. Sposób ten może również ulec zmianie wraz z kolejnymi wersjami klasy - dla użycia klasy ważny jest jedynie jej **interfejs**, czyli funkcje i pola dostępne dla użytkownika klasy. Z tych względów preferowane jest deklarowanie wszystkich pól jako prywatnych, a od tej pory w instrukcjach będą się pojawiały wyłącznie przykłady wykorzystujące klasy.

---
#### Zadanie do realizacji
Dodaj modyfikator *public* do poprzednio utworzonej klasy `Student`. Od tego momentu program powinien działać tak samo jak przed zamianą struktury na klasę.

Zmień modyfikator dostępu do pola `grades` tak, aby zapobiec jego bezpośredniej modyfikacji.

---

## Konstruktor i destruktor

Konstruktor i destruktor to specjalne metody, które - jak wskazują ich nazwy - są wywoływane w momencie odpowiednio tworzenia i usuwania obiektu w pamięci. Mogą one służyć do inicjalizacji pól, alokacji pamięci czy jej zwalniania. Konstruktor ma nazwę taką samą jak nazwa klasy/struktury, a destruktor tę samą nazwę poprzedzoną tyldą (`~`). Domyślnie tworzone są pusty domyślny konstruktor i destruktor.

Konstruktor może mieć dodatkowo argumenty, którymi można na przykład zainicjalizować wartości:

```cpp
class Student {
public:
    Student(std::string n) {
        name = n;
    }
    /* ... */
}
```

Zmienną można w tym momencie stworzyć w następujący sposób:

```cpp
Student s1("Jan");
```

---
#### Zadanie do realizacji

Dodaj do swojej klasy `Student` konstruktor, który umożliwi stworzenie zmiennej typu `Student` i jednoczesne przypisanie mu imienia i nazwiska.

Co się stanie, kiedy spróbujesz utworzyć obiekt nie podając wartości parametrów konstruktora?

```cpp
Student s1;
```

Napraw ten problem dodając domyślną wartość argumentów do konstruktora.

---

## Settery, gettery, nazwy pól i metod

W wielu przypadkach stworzona przez nas klasa będzie miała właściwość (pole), do którego chcemy umożliwić dostęp zarówno do modyfikacji, jak i odczytu. W tym przypadku konieczne będzie stworzenie pary metod, nazywanych często odpowiednio *setterem* i *getterem*. Warto przyjąć konwencję nazw, która pozwoli w czytelny sposób zasugerować do czego służy dana metoda i do których pól się odwołuje, jednocześnie unikając kolizji nazw pól, metod i argumentów do metod. Często można spotkać się z dodawaniem do nazw pól prefixu `m_` bądź suffixu `_`. Nie ma narzuconego standardu, ważne jednak, aby w obrębie własnego kodu trzymać się jednej konwencji. Poniżej przedstawiono prosty przykład klasy z właściwością `index` i najprostszą parą *settera* i *gettera*.

```cpp
class Student {
public:
    void set_index(int index) { // setter
        index_ = index;
    }
    int index() { // getter
        return index_;
    }
private:
    int index_;
};
```

Zadania
------
#### 1. Student

Rozbuduj klasę `Student`, uwzględniając daną funkcjonalność:

* przechowywanie danych osobowych (imię, nazwisko, numer indeksu)
* przechowywanie zbioru ocen

Publiczny interfejs powinien obejmować:

* ustawienie imienia i nazwiska
* ustawienie indeksu
* wyświetlenie podsumowania informacji o studencie (wraz z ocenami)
* dodawanie oceny
* wyznaczenie średniej
* określenie czy student zdał (maksymalnie 1 ocena 2.0)

Pamiętaj, że oceny mogą przyjąć tylko określone wartości, a dopuszczalne numery indeksów mają od 5 do 6 cyfr. Uniemożliw wpisanie niepoprawnych wartości. Wszystkie pola oznacz jako prywatne.

[//]: # (Podziel kod na pliki nagłówkowy z definicją klasy i źródłowy z definicjami metod.)

#### 2. Liczby zespolone

Zaprojektuj klasę `Complex`, która przechowa liczbę zespoloną. Powinna ona mieć konstruktor, który pozwoli na zainicjalizowanie wartości liczby.

Dodaj do niej metody, które pozwolą na:
* odczyt i modyfikację części rzeczywistej oraz urojonej (niezależnie)
* wyświetlenie liczby w czytelnej postaci
* dodanie do jednej liczby drugiej liczby zespolonej oraz rzeczywistej

Poprawnie zaprojektowana klasa powinna pozwolić na uruchomienie poniższego kodu:

```cpp
Complex a(1.0, -2.0); // creates 1-2i
Complex b(3.14); // creates 3.14

b.set_im(-5);

Complex c = a.add(b);

c.print(); // prints 4.14-7i

```

***
Autorzy: *Dominik Pieczyński*, *Jakub Tomczyński*
