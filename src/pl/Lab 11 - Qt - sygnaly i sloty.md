# Qt - sygnały i sloty

## Sygnały i sloty

### Podstawy
Qt implementuje mechanizm sygnałów i slotów. Jest to narzędzie pozwalające na zarządzanie asynchronicznymi (występującymi w losowych momentach bez blokującego wykonywanie kodu oczekiwania na nie) zdarzeniami pochodzącymi zarówno z programu, jak i od użytkownika lub systemu operacyjnego. Wykorzystanie sygnałów (ang. signals) i slotów (ang. slots) jest bardzo proste. Każdy klasa w Qt może implementować sygnały, czyli funkcje emitujące jakieś zdarzenie. Równocześnie każda klasa może implementować sloty, czyli funkcje przechwytujące i przetwarzające powyższe sygnały. Na przykład klasa `QPushButton` posiada sygnał `clicked()`, który jest emitowany w momencie wciśnięcia przez użytkownika przycisku.

Chcąc obsłużyć zdarzenie od przycisku, w klasie głównego okna musimy przygotować odpowiedni slot. W tym celu w deklaracji klasy umieszczamy dyrektywę `slots:` z modyfikatorem dostępu (w tym przypadku `private`), a zaraz pod nią deklarację funkcji która będzie obsługiwać dane zdarzenie:

```cpp
class MainWindow : public QMainWindow {

...
 
private slots:
    void on_button_clicked();
};
```

Następnie w pliku `.cpp` definiujemy naszą funkcję obsługującą zdarzenie. Na razie pozostanie ona pusta:
```cpp
void MainWindow::on_button_clicked() {}
```

**Uwaga:** Na poprzednich zajęciach można było zauważyć, że Qt Creator potrafi sam generować wyżej opisany kod dla kontrolek umieszczonych na formie - zazwyczaj warto korzystać z tej funkcji, jednak dla demonstracji mechanizmu slotów celowo ją pomijamy.

### Łączenie sygnałów do slotów

Sygnały i sloty należy ze sobą połączyć za pomocą dedykowanej funkcji `connect(...)`. Łączy ona dwa obiekty: wysyłający (sygnał) i odbierający (slot). `connect` musi być wywołana tylko raz. Wywołanie to można umieścić np. w konstruktorze klasy. Aby połączyć zdarzenie wciśnięcia przycisku z naszym przygotowanym slotem należy wykonać:

```cpp
connect(ui->pushButton, &QPushButton::clicked, this, &MainWindow::on_button_clicked);
```

Funkcja `connect` w tym przypadku przyjmuje następujące argumenty:
* wskaźnik na obiekt wysyłający sygnał,
* wskaźnik na prototyp funkcji, która stanowi sygnał,
* wskaźnik na obiekt odbierający sygnał,
* wskaźnik na prototyp funkcji, która stanowi slot.

**Uwaga:** jeśli na formie umieścimy przycisk nazywający się `pushButton` (domyślna nazwa) oraz do klasy okna zawierającego przycisk dodamy slot o nazwie `on_pushButton_clicked` to Qt niejawnie (bez wykorzystania w kodzie funkcji `connect`) podłączy sygnał `clicked` do tego slotu. Aby uniknąć tego zachowania (jedynie dla demonstracji) w powyższych przykładach slot nazywa się `on_button_clicked`.

### Dodatkowe informacje
Jeden sygnał może wyzwalać wiele slotów i vice-versa - jeden slot może być wyzwalany przez wiele sygnałów, z różnych obiektów.

Więcej na temat sygnałów i slotów można znaleźć w opisie na [podstronie projektu Qt](https://doc.qt.io/qt-5/signalsandslots.html).

## Menu i QAction

Do klas dziedziczących z `QMainWindow` (zazwyczaj opisujących główne okno programu) standardowo przypisane jest menu użytkownika (`QMenu`), oraz pasek narzędzi. Strukturę menu oraz pasek narzędzi można stworzyć z poziomu programu Qt Creator lub bezpośrednio w kodzie. 

Więcej na ten temat można znaleźć w tutorialu pokazującym jak stworzyć i wykorzystywać modalne okno dialogowe: http://youtu.be/3Are6Iu0XWg oraz w prezentacji: https://drive.google.com/open?id=0B6pDGeM8qbzwOGxJbG1jR3ltcEk. Zwróć uwagę, że od czasu ich przygotowania unowocześnieniu uległ mechanizm sygnałów. Warto stosować ten opisany w niniejszej instrukcji i aktualnej dokumentacji projektu Qt ignorując składnię funkcji `connect` z załączonych filmów i prezentacji.

Powyższe materiały przedstawiają informacje o tym jak:
* tworzyć menu,
* przypisywać daną akcję do różnych elementów (menu, toolbara),
* dodawać ikony do zasobów projektu i przypisywać je do akcji,
* wiązać wywołanie danej akcji ze slotem okna głównego.

## Modalne okna dialogowe
Modalne okna dialogowe to okna, które po wyświetleniu blokują dostęp do okna głównego do czasu aż nie zostaną zamknięte. Mogą zostać wykorzystane do ustawiania parametrów konfiguracyjnych programu. Okna te, zazwyczaj dziedziczą z klasy `QDialog` i zawierają przyciski Ok i Anuluj.

Więcej na ten temat można znaleźć w tutorialu: http://youtu.be/3Are6Iu0XWg oraz w prezentacji: https://drive.google.com/open?id=0B6pDGeM8qbzwX2FwRHNsbXNNUlk

Dowiesz się tam jak:
* tworzyć okno dialogowe wykorzystując Qt Creator,
* wyświetlać okno dialogowe i wykrywać w jaki sposób zostało zamknięte (czy użytkownik zaakceptował wprowadzone zmiany),
* wykorzystać okno dialogowe do zmiany parametrów przechowywanych w klasie okna głównego zachowując hermetyzację.

## Dynamiczne tworzenie okien
Okna można tworzyć z użyciem programu Qt Creator jak i bezpośrednio w kodzie przydzielając dynamicznie pamięć dla wszystkich komponentów okna. 

Qt posiada własny mechanizm zarządzania pamięcią, w którym każdy obiekt dziedziczący z klasy `QObject` przechowuje adres rodzica (obiektu nadrzędnego). Jeśli w programie następuje zwolnienie pamięci dla rodzica automatycznie zwalniana jest również pamięć dla wszystkich obiektów podrzędnych.

Więcej na ten temat można znaleźć w tutorialu pokazującym jak w Qt stworzyć wirtualną klawiaturę: http://youtu.be/UVg28aVpBXI oraz w prezentacji: https://drive.google.com/open?id=0B6pDGeM8qbzwbWJ4bzljVlpaMHc

Dowiesz się tam jak:
* tworzyć elementy okien z poziomu kodu programu,
* wykorzystywać layouty.

**Uwaga:** zignoruj informacje na temat łączenia sygnałów w starszy sposób oraz wykorzystanie klasy `QSignalMapper`, której użycie nie jest już zalecane. Można ją zastąpić na przykład przekazaniem do funkcji `connect` funkcji lambda. Przykład takiego wywołania pochodzący z [dokumentacji wspomnianej klasy](https://doc.qt.io/qt-5/qsignalmapper.html#details):
```cpp
ButtonWidget::ButtonWidget(const QStringList &texts, QWidget *parent)
    : QWidget(parent) {
    QGridLayout *gridLayout = new QGridLayout;
    for (int i = 0; i < texts.size(); ++i) {
        QString text = texts[i];
        QPushButton *button = new QPushButton(text);
        connect(button, &QPushButton::clicked, [=] { clicked(text); });
        gridLayout->addWidget(button, i / 3, i % 3);
    }
    setLayout(gridLayout);
}
```

W powyższym przykładzie slot `clicked` jest wywoływany z argumentem `text`.

## Przebieg zajęć
<!--Zapoznaj się z dwoma tutorialami i spróbuj zrealizować samodzielnie te same zadania:
* Do projektu dodaj modalne okno dialogowe oraz menu i pasek narzędzi. (http://youtu.be/3Are6Iu0XWg )
* Stwórz wirtualną klawiaturę o przełączanym układzie (klawiatura literowa i numeryczna) (http://youtu.be/UVg28aVpBXI)
* Spróbuj dodać do drugiego projektu okno dialogowe umożliwiające zmianę liczby kolumn klawiatury numerycznej i literowej.-->

### Edytor tekstu

Wykorzystując Qt napisz prosty edytor tekstu.

Edytor powinien pozwalać na:
* utworzenie nowego dokumentu
* otwarcie pliku
* nadpisanie pliku 
* zapisanie pliku jako nowy

Dodaj odpowiednie widgety do okienka i akcje do menu *File*.

Do wskazania plików wykorzystaj klasę [QFileDialog](https://doc.qt.io/qt-5/qfiledialog.html).

Wczytaj całą zawartość otwartego pliku do okienka tekstowego (do operacji na plikach służy klasa [Qfile](https://doc.qt.io/qt-5/qfile.html)).

Umożliw nadpisanie pliku (zapisanie pod tą samą ścieżką) lub zapisanie jako nowy plik.

***

Następnie dodaj do programu funkcjonalność *Find and replace*. Utwórz klasę okienka (*File* → *New File or Project* → *Qt* → *Qt Designer Form Class* → *Widget*), w którym będziesz mógł wprowadzić dwa ciągi znaków (źródłowy i docelowy) oraz dwa przyciski: *Replace* i *Replace all*.

Dodaj menu *Edit*, a w nim akcję *Find and replace*.

Połącz ich sygnały ze slotami w głównym okienku i zaimplementuj wymaganą funkcjonalność.

Możesz wspomagać się tutorialem [Application Example](https://doc.qt.io/qt-5/qtwidgets-mainwindows-application-example.html).

***
Autorzy: *Tomasz Mańkowski*, *Jakub Tomczyński*, *Dominik Pieczyński*
