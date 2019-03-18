Dziedziczenie i obsługa zdarzeń w SFML
=======================================

Dziedziczenie
-----------
W ramach kursu stworzyliśmy przykladowe klasy opisujące mogące reprezentować byty z rzeczywistego świata (np. klasa `Student`). Wiele typów rzeczywistych obiektów może współdzielić pewne cechy bądź należeć do wspólnej, większej grupy. Przykładowo *samochody*, *motocykle*, *hulajnogi* i *rowery* są przykładami *pojazdów*. Oznacza to, że będą współdzieły pewne cechy. Przekładając to na język programowania obiektowego, możemy powiedzieć, że klasy `Car`, `Motorcycle`, `Scooter` i `Bike` dziedziczą właściwości klasy `Vehicle`. Mechanizm ten nazywa się *dziedziczeniem* (ang. *inheritance*) i pozwala nam nie tylko na ustalenie pewnej hierarchicznej zależności pomiędzy tymi klasami, ale przede wszystkim ustalenie wspólnego *interfejsu*, który powinien być zaimplementowany we wszystkich *klasach potomnych*, jednocześnie unikając niepotrzebnego powielania kodu. 

Przykład klasy *bazowej* (ang. *base*):
```cpp
class Vehicle {
public:
  std::string name() { return name_; }
  int number_of_wheels() { return number_of_wheels_; }
  std::string propulsion_type() { return propulsion_type_; }
  double max_speed() { return max_speed_; }

protected:
  Vehicle(const std::string &name, int number_of_wheels,
          const std::string &propulsion_type, double max_speed)
      : name_(name), number_of_wheels_(number_of_wheels),
        propulsion_type_(propulsion_type), max_speed_(max_speed) {}

  std::string name_;
  int number_of_wheels_;
  std::string propulsion_type_;
  double max_speed_;
};
```
Główna zmiana w stosunku do klas, które implementowaliśmy poprzednio, to nowy modyfikator dostępu *protected* użyty w klasie `Vehicle`. Zachowuje się on podobnie do modyfikatora *private* (unuiemożliwia dostęp z poza klasy), ale w przeciwieństwie do niego umożliwia dostęp do pól/metod z poziomu klas, które dziedziczą z wyżej opisanej klasy. Działanie wszystkich trzech modyfikatorów opisuje poniższa tabela:

| Dostęp z poziomu | public | protected | private |
|:--------------------------:|:------:|:---------:|:-------:|
| członków tej samej klasy |   tak  |    tak    |   tak   |
|  członków klasy potomnej |   tak  |    tak    |    nie   |
| spoza klasy |   tak  |     nie    |    nie   |

Możesz zauważyć, że konstruktor klasy `Vehicle` oznaczony jest jako *protected* (chroniony). Ma to miejsce, ponieważ nie chcemy umożliwiać stworzenia instancji obiektu klasy `Vehicle` bezpośrednio. Użyjemy jej jako "szablonu" dla poszczególnych klas potomnych.

Przykładową klasą dziedziczącą z klasy `Vehicle` jest klasa `Bike`:
```cpp
class Bike : public Vehicle {
public:
  Bike() : Vehicle("Bike", 2, "Muscles", 30) {}
};
```
Klasy bazowe podajemy po dwukropku przy deklaracji klasy potomnej. Widzimy, że klasa `Bike` dziedziczy z klasy `Vehicle` z modyfikatorem *public*. Podczas dziedziczenia modyfikatory mają następujący efekt:
* *public*: wszystkie pola z klasy bazowej zachowują swój poziom dostępu,
* *protected*: publiczne pola i metody klasy bazowej stają się chronione w klasie potomnej,
* *private*: chronione i publiczne pola klasy bazowej stają się prywatne w klasie potomnej.

Klasa `Bike` definiuje domyślny konstruktor, który wywołuje konstruktor klasy bazowej z pewnymi stałymi parametrami. Dokonujemy pewnych założeń co do rowerów: nie mają specjalnych nazw, mają zawsze dwa koła, używają tylko siły mięśni jako źródła napędu i mają prędkość maksymalną równą 30. Klasa `Bike` nie dodaje żadnej funkcjonalności do klasy bazowej, ale możliwe jest już utworzenie jej instancji. Możemy np. odwoływać się do *getterów* klasy bazowej:

```cpp
Bike bike;
std::cout << bike.max_speed() << std::endl; // Will print 30
```

Możemy zdefiniować kolejną klasę potomną - `Car`:
```cpp
class Car : public Vehicle {
public:
  Car(const std::string &name, const std::string &propulsion_type,
      double max_speed, bool has_abs)
      : Vehicle(name, 4, propulsion_type, max_speed),
        has_abs_(has_abs) {}

  bool has_abs() { return has_abs_; }

private:
  bool has_abs_;
};
```

Klasa `Car` jest nieco bardziej skomplikowana: ma konstruktor, który przyjmuje argumenty i przekazuje je do konstruktora klasy bazowej, zakładając jedynie stałą wartość liczby kół równą 4. Dodatkowo klasa definiuje właściwość `has_abs_` wraz z getterem, która również jest inicjalizowana w konstruktorze. Jest to właściwość specyficzna dla samochodów, nie współdzielona z innymi typami pojazdów.

Po utworzeniu instancji obiektu klasy `Car` możemy się odwoływać do metod i pól zarówno klasy bazowej, jak i dodanych w klasie potomnej:
```cpp
Car passat("Volkswagen Passat", "Diesel", 200, true);
std::cout << "Name: " << passat.name() << std::endl;
std::cout << "Has ABS: " << passat.has_abs() << std::endl;
```

Klasy mogą również dziedziczyć z wielu klas bazowych, otrzymując zbiór wszystkich właściwości i metod klas bazowych. Nazywa się to wielodziedziczeniem i może zostać zapisane w następujący sposób: `class Car : public Object, public Vehicle`.

Oczywiście poruszyliśmuy jedynie fragment zagadnienia, jakim jest dziedziczenie. Dziedziczenie daje wiele możliwości, np. tworzenie kontenerów polimorficznych - przechowujących wskaźniki do różnych klas, które mają wspólną klasę bazową.

---
#### Zadanie do realizacji
Bazując na powyższym opisie i przykładach dziedziczenia, stwórz klasę `CustomRectangleShape` dziedzicząc z klasy `sf::RectangleShape`.
Rozszerz funkcjonalność klasy bazowej dodając odpowiednie właściwości:

* prędkość pozioma i pionowa
* prędkość obrotowa
* wymiary "pola gry" w pikselach

Dodaj do  `CustomRectangleShape` dodatkową metodę `step`. Powinna ona przyjmować czas, jaki upłynął od ostatniego kroku animacji, powodować wykonanie pojedynczego kroku animacji (przesunięcie i obrót), a także powodować odbicie od napotkanych krawędzi ekranu.

Stwórz w programie obiekt  `CustomRectangleShape`. Główna pętla programu powinna wywoływać metodę `step`, a efekt powinien być podobny do programu z ostatnich zajęć.

---

Obsługa zdarzeń w SFML
-----------------------
Większość gier komputerowych musi reagować na zewnętrzne zdarzenia wejścia - generowane przez np. klawiaturę lub mysz. Naciśnięcie klawisza na klawiaturze lub przesunięcie myszy - to *zdarzenia* (ang. *event*), które system operacyjny przekazuje aplikacji. Biblioteka SFML obsługuje [różne typy zdarzeń](https://www.sfml-dev.org/documentation/2.5.1/classsf_1_1Event.php) przez dedykowane klasy.

Do obsługi klawiatury i myszy SFML oferuje klasy [sf::Keyboard](https://www.sfml-dev.org/documentation/2.5.1/classsf_1_1Keyboard.php) oraz [sf::Mouse](https://www.sfml-dev.org/documentation/2.5.1/classsf_1_1Mouse.php). Są to klasy statyczne, co oznacza, że nie możemy utworzyć ich instancji - w programie istnieje jedna, globalna instancja każdej z tych klas i możemy odwoływać się do jej metod z dowolnego miejsca w programie. Wynika to ze specyfiki urządzeń wejścia - nawet, jeśli podłączymy do komputera kilka myszy lub klawiatur, z punktu widzenia aplikacji są widoczne jako jedno źródło zdarzeń.

Wskazana powyżej dokumentacja opisuje metody, które pozwalają na sprawdzenie czy jakieś zdarzenie ma obecnie miejsce (np. czy wciśnięty jest klawisz) oraz jego szczegóły.

Zadania
-----------
#### 1. Przesuwanie prostokąta naciśnięciami klawiszy
Zmień metodę `step` klasy `CustomRectangleShape` tak, aby prostokąt poruszał się tylko w momencie naciśnięcia klawiszy strzałek na klawiaturze: naciśnięcie jednej ze strzałek powoduje poruszanie się prostokąta we wskazanym kierunku, zgodnie z prędkością przechowywaną wewnątrz klasy. Pamiętaj, aby zabezpieczyć prostokąt przed opuszczeniem obszaru okna.

Kiedy użytkownik kliknie lewym przyciskiem myszy wewnątrz obszaru prostokąta, powinien on zmienić kolor na losowy.

#### 2. Wybór przesuwanego prostokąta

Dodaj do sceny 10 prostokątów w losowych pozycjach (w obrębie okna), umieść je w kontenerze. Zmodyfikuj kod z poprzedniego zadania tak, aby kliknięcie myszą pozwalało na wybór prostokąta, który będzie poruszał się zgodnie z klawiszami strzałek: kliknięcie w prostokąt powinno powodować jego podświetlenie na kolor inny od pozostałych. Wyróżniony prostokąt porusza się zgodnie z klawiszami strzałek, pozostałe pozostają nieruchomo.

**Podpowiedź:** konieczne może być dodanie dodatkowej właściwości i settera do klasy `CustomRectangleShape` tak, aby obiekt "wiedział" czy jest aktualnie wybrany.

***
Autorzy: *Dominik Pieczyński*, *Jakub Tomczyński*
