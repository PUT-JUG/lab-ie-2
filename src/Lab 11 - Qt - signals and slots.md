# Qt - signals and slots

## Signals and slots

### The basics
Qt implements a mechanism of signals and slots. It is a tool that allows you to manage asynchronous events (occurring in random moments without blocking the execution of the code that waits for them) from the program, the user or the operating system. The use of signals and slots is very simple. Every class in Qt can implement signals, i.e. functions emitting an event. At the same time each class can implement slots, i.e. functions that capture and process the above signals. For example, a `QPushButton` class has a `clicked()` signal that is emitted when the user presses a button.

In order to handle the event from the button, in the main window class we need to prepare a suitable slot. For this purpose, in the class declaration we put the directive `slots:` with access modifier (in this case `private`), and immediately below it the declaration of the function that will handle the event:

```cpp
class MainWindow : public QMainWindow {

...
 
private slots:
    void on_button_clicked();
};
```

Then in the `.cpp` file we define our event handling function. For now, it will remain empty:
```cpp
void MainWindow::on_button_clicked() {}
```

**Note:** During the previous classes you could notice that Qt Creator can generate the code described above for the controls placed on the form - usually it is worth to use this function, but for the demonstration of the slots mechanism we deliberately omit it.

### Connecting signals to slots

Signals and slots should be connected together using a dedicated `connect(...)` function. It connects two objects: the sender (signal) and the receiver (slot). The `connect` must be called only once. This call can be placed e.g. in a class constructor. In order to connect a button pressed event with our prepared slot, it is necessary to make a call:

```cpp
connect(ui->pushButton, &QPushButton::clicked, this, &MainWindow::on_button_clicked);
```

The `connect` function takes the following arguments in this case:
* a pointer to the object sending the signal,
* a pointer to the prototype of the function that constitutes the signal,
* a pointer to the object receiving the signal,
* a pointer to the prototype of a function that is a slot.

**Note:** if we put a button called `pushButton` (default name) on the form and add a slot called `on_pushButton_clicked` to the class of the window containing the button, Qt will implicitly (without using the `connect` function in the code) connect the `clicked` signal to this slot. To avoid this behavior (only for demonstrations), the slot is called `on_button_clicked` in the above examples.

### Additional information
One signal can trigger multiple slots and vice-versa - one slot can be triggered by multiple signals from different objects.

More about signals and slots can be found in the description on the [Qt project subpage](https://doc.qt.io/qt-5/signalsandslots.html).

## Menu and QAction

For classes inheriting from `QMainWindow` (usually describing the main window of the program), a user menu (`QMenu`) and a toolbar are assigned by default. The menu structure and toolbar can be created from Qt Creator or directly in the code. 

More about menus and actions can be found in [Menus example](https://doc.qt.io/qt-5/qtwidgets-mainwindows-menus-example.html).

## Modal dialog boxes
Modal dialog boxes are windows that, when displayed, block access to the main window until they are closed. They can be used to set the configuration parameters of the program. These windows, usually inherit from the `QDialog` class and contain the Ok and Cancel buttons.

More information about this can be found in [Dialogs example code](https://doc.qt.io/qt-5/qtwidgets-dialogs-standarddialogs-example.html).

## Dynamic window creation
Windows can be created using Qt Creator or directly in the code, dynamically assigning memory to all components of the window. 

Qt has its own memory management mechanism, in which every inherited object from the `QObject` class stores the parent's address. If the program releases the memory for the parent, the memory for all children objects is also released automatically.

For example in the following code:
```cpp
QObject *parent = new QObject();
QObject *child1 = new QObject(parent);
QObject *child2 = new QObject(parent);
QObject *child1_1 = new QObject(child1);
QObject *child1_2 = new QObject(child1);
delete parent;
```
deleting the parent also deletes all of the children.  

## Assignments

### Text editor

Use Qt to write a simple text editor.

The editor should allow you to:
* create a new document
* open a file
* overwrite the file 
* save the file as a new one.

Add appropriate widgets to the window and actions to the *File* menu.

Use the [QFileDialog](https://doc.qt.io/qt-5/qfiledialog.html) class to select files.

Load the entire contents of the open file into the text window (the [QFile](https://doc.qt.io/qt-5/qfile.html) class is used for file operations).

Allow to overwrite the file (save under the same path) or save it as a new file.

***

Add *Find and replace* to the program. Create a window class (*File* → *New File or Project* → *Qt* → *Qt Designer Form Class* → *Widget*) where you can enter two strings of characters (source and target) and two buttons: *Replace* and *Replace all*.

Add the *Edit* menu with the *Find and replace* action.

Connect their signals to the slots in the main window and implement the required functionality.

You can find some implementation details in the [Application Example](https://doc.qt.io/qt-5/qtwidgets-mainwindows-application-example.html).

***
Authors: *Tomasz Mańkowski*, *Jakub Tomczyński*, *Dominik Pieczyński*
