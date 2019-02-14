Wstęp do klas
=============

Proste struktury i funkcje w stylu języka C
-------------------------------------------
Do tej pory tworzone na zajęciach struktury zawierały jedynie pola będące zmiennymi różnych typów. Jako przykład można potrakować strukturę `Student`:
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

Takie podejście niesie ze sobą pewne konsekwencje: szczególnie w większych projektach powstaje dużo wolnych, globalnie dostępnych funkcji o różnych nazwach, które nie są hierarchicznie ułożone.

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
Napisz funkcję `print` wewnątrz struktury `Student`, która wydrukuje imię i nazwisko studenta oraz wszystkie jego oceny:
```
Jan Kowalski: 3.0 4.5 5.0 3.5
```

---

### Weryfikacja poprawności wprowadzanych danych
Napisany program w chwili obecnej nie dokonuje żadnego sprawdzania wprowadzanych danych. Studentowi można przypisywać kolejne oceny, które będą dowolnymi liczbami.

Przykładowa metoda (wewnątrz struktury `Student`), która umożliwia dodanie nowej oceny wraz z weryfikacją jej poprawności może wyglądać następująco:
```cpp
bool add_grade(float grade) {
if (grade >= 2.0 and grade <= 5.0) {
// The grade is valid; let's add it and return true
grades.push_back(grade);
return true;
}

// The grade is invalid; let's return false
return false;
}
```

Powyższa metoda nie rozwiązuje jednak wszystkich problemów. Do obiektu struktury `Student` nadal można dodać ocenę pomijając wywołanie `add_grade`:
```cpp
Student student;
student.grades.push_back(8.0);
```

Dodatkowo student może być utworzony z ocenami z błędnego przedziału:
```cpp
Student student{"Jan", "Kowalski", {5, 10, 15}};
```

Korzystanie z tak przygotowanego interfejsu wymaga dużej samodyscypliny oraz przygotowania dokumentacji informującej osobę mającą używać takiego kodu o konieczności dodawania ocen tylko z użyciem metody `add_grade`.

Klasy jako alternatywa struktur
--------------------------------
Problemy opisane powyżej mogą być rozwiązane przy użyciu klas. Koncepcyjnie klasy przypominają struktury: również posiadają pola i metody. Różnica jest zauważalna dopiero przy próbie uzyskania dostępu do pól lub metod.

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

natomiast deklaracja następującej klasy:
```cpp
class Student {
std::string name;
std::string surname;
}
```
jest równoznaczna następującej deklaracji struktury:
```cpp
struct Student {
private:
std::string name;
std::string surname;
}
```

Ze względu na to, że domyślny, prywatny poziom dostępu do pól i metod klasy jest uznawany za bezpieczniejszy od publicznego, od tej pory w instrukcjach będą się pojawiały wyłącznie przykłady wykorzystujące klasy.

---
#### Zadanie do realizacji
Dodaj modyfikator *public* do poprzednio utworzonej klasy `Student`. Od tego momentu program powinien działać tak samo jak przed zamianą struktury na klasę.

Zmień modyfikator dostępu do pola `grades` tak, aby zapobiec jego bezpośredniej modyfikacji.

---
