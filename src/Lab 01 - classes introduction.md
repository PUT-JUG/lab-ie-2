# Classes introduction

## Simple C style structures and functions

Up to that point we only used structures containing fields that were variables of different types. `Student` structure can be an example:

```cpp
struct Student {
    std::string name;
    std::string surname;
    std::vector<float> grades;
};
```

Assuming that we want to calculate an average of grades we should write a function that will perform this operation for the object of `Student` structure:

```cpp
float calculate_grade(const Student &student) {
    float sum = std::accumulate(student.grades.begin(), student.grades.end(), 0.0f);
    return sum / student.grades.size();
}
```

This approach has some side effects: especially in big projects there is a lot of free (not belonging to any structure), globally accessible functions with different names and no hierarchy.

## Simple C++ style structures

### Basic information

Functions associated with given structure can be declared inside of its' declaration. Such function is called **method** and has access to all values currently stored in the object of that structure.

Modified declaration of `Student` structure can look as follows:

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

This time `calculate_grade` method is declared inside the `Student` structure and doesn't have any parameters. Despite that fact, it can still access values of the `Student` object fields.

Referencing methods declared inside structures is very similar to referencing fields:

```cpp
Student student{"Some", "Student", {2, 3, 4, 5, 3}}; // This creates object of Student type
std::cout << student.calculate_grade() << std::endl; // This calls calculate_grade function and prints the result
```

---

#### 🛠🔥 Assignment 🔥🛠

Add a `print` method that prints the student's name and all his grades inside the `Student` structure:

> Jan Kowalski: 3.0 4.5 5.0 3.5

---

### Verifying input

Our program does not check the input data correctness at the moment. One can assign grades from any range to students. 

Example method (inside `Student` structure), that allows to add new grade while verifying it can look as follows:

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

The solution above however does not solve all the problems. We can still add grades to the `Student` structure object while skipping the call to `add_grade`:

```cpp
Student student;
student.grades.push_back(8.0);
```

Additionally, `Student` variable can be initialized with the values from invalid range:

```cpp
Student student{"Jan", "Kowalski", {5, 10, 15}};
```

Using such an interface is error prone and requires a lot of self discipline from the programmer. Additionally, some documentation explaining the need of adding grades only via `add_grade` method should be prepared.

## Classes as structures alternative

The problems described above can be solved by using classes. Conceptually classes are very similar to the structures: they also have fields and methods. The classes allow person that designs them to limit the possibilities of accessing them from "outside".

---

#### 🛠🔥 Assignment 🔥🛠

Change the `struct Student` declaration to `class Student`. Try to compile the code that accesses the fields or methods of the `Student` class.

---

There are three different access modifiers that control the access the the fields and methods of the structures and classes: *public*, *protected* and *private*. We will skip the *protected* modifier for now.

* The *public* modifier means, that fields and methods marked with this modifier can be freely accessed from outside of a structure or class. **All fields and methods of structures all public by default**.
* The *private* modifier means, that fields and methods marked with this modifier can only be accessed from the code that is inside the methods of the same structure or class. **All fields and methods of classes are private by default**.

The only difference between classes and structures in C++ is the default access modifier. In fact, the declaration of the following structure:

```cpp
struct Student {
    std::string name;
    std::string surname;
};
```

is equivalent to the following class declaration:

```cpp
class Student {
public:
    std::string name;
    std::string surname;
};
```

The modifier is applied to all fields and methods declared below it until new modifier is encountered.

The additional benefit of protecting the fields and allowing accessing them only by public methods is that person using such a class doesn't need to be concerned about internal implementation details. The way the data is stored inside of the class can change with time, while so called public **interface** (methods and fields available outside) will remain the same. For that reason it is advised to declare all fields as private. From that point the instructions will use classes instead of the structures.

---

#### 🛠🔥 Assignment 🔥🛠

Add *public* modifier to the `Student` structure declared previously. From that moment your program should work the same as before changing the structure into the class.

Change the `grades` field access modifier so that it won't be possible to modify the grades directly anymore.

---

## Constructor and destructor

Constructor and destructor are special methods that - as their names suggest - are called during the construction and destruction of the object in memory. They can be used for fields initialization, memory allocation or memory deallocation. The constructor has the same name as the class and the destructor has the same name as the classes but preceded by tilde (`~`) character. The default constructors and destructors are empty.

Constructors can additionally take some arguments, that can be used for fields initialization:

```cpp
class Student {
public:
    Student(std::string n) {
        name = n;
    }
    /* ... */
}
```

The variable (object) can be then created this way:

```cpp
Student s1("Jan");
```

---

#### 🛠🔥 Assignment 🔥🛠

Add constructor to your `Student` class. It should allow creating `Student` object and assigning name and surname at the same time.

What will happen if you try to create the object without providing constructor arguments?

```cpp
Student s1;
```

Fix that issue by adding default argument values to the constructor.

---

## Setters, getters, fields names and methods

In many cases our class will have some property (field) that we want to both read and modify. In that case there will be a need to create a pair of methods called *getter* and *setter*. It is a good habit to use a name convention that will clearly suggest what the given method does and which fields it accesses. At the same time it will allow to avoid name collisions between fields, methods and methods' arguments. Sometimes fields are prefixed with `m_` or suffixed with `_`. There is no standard, but the convention should be kept the same in one code base. The simple example is presented below. The class has a property called `index` and simple *getter* and *setter*.

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

## Final assignments 🔥🛠

#### 1. Student

Extend `Student` class to have following functions:

* storing personal data (name, surname, album number)
* storing multiple grades

Public interface should contain:

* setting name and surname
* setting album number
* displaying student summary (along with grades)
* adding new grades
* calculating the mean grade
* function that checks if the student passed the semester (only one 2.0 grade is allowed)

Remember that the grades have to be from a valid range and album number contains from 5 to 6 digits. Disallow entering invalid data. Make all fields private.

[//]: # (Podziel kod na pliki nagłówkowy z definicją klasy i źródłowy z definicjami metod.)

#### 2. Complex numbers

Create `Complex` class, that will store complex number. It should contain constructor that will allow initialization of the number value.

Add methods that will allow:
* reading and modifying real and imaginary part of the number (separately)
* displaying the number in readable format
* adding complex or real number to the current one

Correctly designed class should allow running the following code:

```cpp
Complex a(1.0, -2.0); // creates 1-2i
Complex b(3.14); // creates 3.14

b.set_im(-5);

Complex c = a.add(b);

c.print(); // prints 4.14-7i

```

---
Authors: *Dominik Pieczyński*, *Jakub Tomczyński*
