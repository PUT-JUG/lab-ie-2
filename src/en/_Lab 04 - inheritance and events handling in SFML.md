Inheritance and events handling in SFML
=======================================

Inheritance
-----------
We already prepared some classes that described some real-life entities (the `Student` class can be an example). Some entities in real life stay in relations. For example cars, motorcycles, scooters and bikes are all different kinds of vehicles. It means that all of them have some common properties. In object-oriented programming we would tell that  `Car`, `Motorcycle`, `Scooter` and `Bike` classes inherit `Vehicle` properties. The described mechanism is called *inheritance* and it allows us to describe some hierarchical relations, but, most importantly, it allows as to define some common interface that multiple classes should implement, while avoiding unnecessary code duplication.

Let's take a look at an example base class:
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
Most importantly, the `Vehicle` class uses new access modifier called *protected*. It behaves like the *private* modifier (disables access from outside of the class), but what's different is that it allows classes that inherit the base class to access the properties/methods, while *private* does not. The table below describes how different modifiers work in classes:

|                    Access | public | protected | private |
|:--------------------------:|:------:|:---------:|:-------:|
| members of the same class |   yes  |    yes    |   yes   |
|  members of derived class |   yes  |    yes    |    no   |
|               non-members |   yes  |     no    |    no   |

You can notice that the constructor of the `Vehicle` class is marked as *protected*. This is done because we don't want to create the objects of `Vehicle` class directly. We will rather use the classes that inherit it.

The first example that inherits the `Vehicle` class is the `Bike` class:
```cpp
class Bike : public Vehicle {
public:
  Bike() : Vehicle("Bike", 2, "Muscles", 30) {}
};
```
Base classes are listed following a colon during class declaration. We can see that it inherits the `Vehicle` class with *public* modifier. When inheriting classes the modifiers have the following effects:
* *public*: the public and protected members of the base class listed after the specifier keep their access level in the derived class,
* *protected*: the public and protected members of the base class listed after the specifier become protected members of the derived class,
* *private*: the public and protected members of the base class listed after the specifier become private members of the derived class.

The `Bike` class defines a constructor without arguments that always calls the `Vehicle` constructor with the same parameters. We make some assumptions about the bikes: they don't have any special names, always have two wheels, don't use propulsion mechanisms other than human muscles and achieve the maximum speed of 30 units. This class doesn't add any functionality to the one the base class offers. We can call base class getters to read the properties of the base class:
```cpp
Bike bike;
std::cout << bike.max_speed() << std::endl; // Will print 30
```

We can also define another derived class - `Car`:
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
This one is slightly more complex. It has constructors that takes some arguments and passes them to the base class constructor, while filling some others with constant values (like four wheels). It also initializes additional field of that class: `bool has_abs_`. This is an additional property that is not common for all vehicles, but specific to cars.

We can access both the inherited and additional properties after constructing a `Car` object:
```cpp
Car passat("Volkswagen Passat", "Diesel", 200, true);
std::cout << "Name: " << passat.name() << std::endl;
std::cout << "Has ABS: " << passat.has_abs() << std::endl;
```

The class that we write can also inherit properties and methods from multiple classes. In such case we just need to write other base classes names after the class name in the declaration. For example: `class Car : public Object, public Vehicle`.

Of course we only scratched the surface of inheritance possibilities and applications with those examples. People usually use inheritance with virtual functions, polymorphism and polymorphic containers (containers that can store pointers to objects of different types that have common base class).

---
#### Assignment
Based on the description and examples above, create `CustomRectangleShape` class by inheriting the `sf::RectangleShape` and extending it's content with object's vertical collision border, vertical speed, horizontal collision border, horizontal speed and rotation speed.

Add a new `step` method to `CustomRectangleShape`. It should take the time that passed since last step as an argument and perform a step of animation (translation and rotation).

Use the class to create and draw the rectangle. The main loop of the program should call the `step` method.

---

Events handling in SFML
-----------------------
Most computer games need to react to some external events - mainly those generated by keyboard or mouse. Pressing some keyboard key as well as moving a mouse is an event. The SFML library supports [different kinds of events](https://www.sfml-dev.org/documentation/2.5.1/classsf_1_1Event.php) through special classes for different devices.

For the keyboard and mouse SFML offers [sf::Keyboard](https://www.sfml-dev.org/documentation/2.5.1/classsf_1_1Keyboard.php) and [sf::Mouse](https://www.sfml-dev.org/documentation/2.5.1/classsf_1_1Mouse.php) classes. They are static, which means that we cannot create objects from them - they only have one, global instance with one, shared state. This is because typically only one keyboard and mouse is connected to the computer.

The linked documentation for event classes lists methods that allow to check if some event is currently occurring and what are its' details.

Assignments
-----------
#### 1. Moving the rectangle with keyboard
Change the `step` method of `CustomRectangleShape` class to not move the rectangle by itself, but to react to keyboard and mouse events.

When user presses any arrow on the keyboard the rectangle should move in the chosen direction with the speed that is stored in the class field. Remember that the rectangle should not cross the window boundaries.

When user clicks the left mouse button while the mouse position intersects with the rectangle, the rectangle should change its' color to a random one.

#### 2. Moving selected rectangle
Add multiple rectangles with random positions (but inside the window boundaries) to the scene. Change the previous code to allow the use of a mouse to select the rectangle that will move when arrows are pressed on a keyboard.

When the user selects a rectangle it should be highlighted with different color than the other ones. After selecting another rectangle the highlighting should disappear from the previous one.

**Hint:** you may need to add additional property and setter to your `CustomRectangleShape` class to keep the rectangle informed about being currently selected.

***
Autorzy: *Dominik Pieczyński*, *Jakub Tomczyński*
