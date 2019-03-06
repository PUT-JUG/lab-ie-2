Classes - operators, conversions
================================

Rational class
-------------------------------------------
Let's design `Rational` class that will operate on decimal fractions. We can divide programming of such class into three stages:

### Establishing requirements for the class

* it should store decimal fractions (integral numerator and integral denominator)
* it should offer operations such as addition and subtraction
* it should display the fraction as a normal number if the denominator is equal to 1, in other case in "#/#" notation

### Establishing the *interface*

The class should contain methods allowing:
* setting numerator and denominator of the fraction
* getting the values of enumerator and denominator
* setting the values of enumerator and denominator separately
* performing mathematical operations
* displaying the value of fraction

### Implementation

Let's split the code into header and source file. If the method's implementation is located outside of the class declaration, its' name should be preceded with `ClassName::` to show the compiler that the function we are defining is a method from some class and not a free function. Additionally, methods that don't cause any changes in the object they are called from (such as getters, display function) should be marked with `const` modifier placed after the arguments list. It will allow to call this method in a context to which the object of the class containing such method was passed as *const*. The example implementation is shown below: 

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
Rational::Rational(int num, int den) : num_(num) { // initializes num_ field with num value
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

Check how the class is working using the code below:

```cpp
Rational quarter(1, 4);
Rational one_third(1, 3);

Rational add_result, sub_result;
add_result = one_third.add(quarter);
sub_result = one_third.subtract(quarter);

one_third.print(); std::cout << " + "; quarter.print(); std::cout << " = "; add_result.print(); std::cout << std::endl;
one_third.print(); std::cout << " - "; quarter.print(); std::cout << " = "; sub_result.print(); std::cout << std::endl;
```


Operator overloading
-----------------------------------

The previously created class allows performing operations on fractions, but is not as convenient as built-in C++ types:
```cpp
int a = 1, b = 2;
int c = a + b;
```
C++ language allows defining (overloading) operators in custom classes in order for them to perform operations defined by programmer. Functions that overload operators can take as many arguments as they take in case of built-in types. The precedence of the operators works the same as with built-in types. It means that the order of `d = a + b * c` execution always looks like this: `a + (b * c)`. The operators are only graphic symbols and they can perform any operations; however, it is advised for the overloaded operators to perform operations similar to the original ones to keep the clarity. For example: `+` operator performing printing or subtraction may by very confusing.

The declaration of operator overload looks similar to a method declaration:

```cpp
return_type operator#(arguments list)
```
where `#` is the overloaded operator

Example operators of addition and subtraction for the `Rational` class can look following (similar to `add` and `subtract` methods):

```cpp
Rational operator+(const Rational &rhs) const; // rhs - Right Hand Side - right operand
Rational operator-(const Rational &rhs) const;
```

Please note, that the operators above always take only one argument - the right operand. Operator, just like methods `add` and `subtract` is executed *on the object* that is the left operand:

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
The code performing the computations can look as follows:
```cpp
add_result = one_third + quarter;
sub_result = one_third - quarter;
```

---
#### Assignment
Add the operators mentioned above to the `Rational` class.
Prepare the implementation of multiplication operator (`*`). Check if it works correctly.

---

### Friend functions, stream operator

The overloaded operators purpose is not always exactly the same as the original one. For example: operators `<<` and `>>` are normally used to perform binary shifts on numbers, but because of their graphical resemblance to arrows, in C++ they are used for streams.

Displaying the value of the fraction using the `print()` method is not very convenient. Additionally, it doesn't allow writing to the text file. The better way could look like this:

```cpp
std::cout << one_third << " - " << quarter << " = " << sub_result << std::endl;
```

In the case of `<<` operator the left operand is the *stream* and the right operand is the element added to the stream. The example of `+` and `-` operators show that the left operand is predetermined in a way - it is the object of class that we created the overload in. We can't simply modify the stream class, as it is the part of standard library.

In such case we can write a free function that will take the *stream* and our fraction object as the arguments and return modified stream:

```cpp
std::ostream &operator<<(std::ostream &str, Rational &rhs){
    if (rhs.den_ == 1) {
        str << rhs.num_;
    } else {
        str << rhs.num_ << "/" << rhs.den_;
    }
    return str;
}
```

Add the code above to the `Rational.cpp` file.

Free functions, as we already know, cannot access private fields of a class. Because of that fact we need to declare a friendship between class and function. Add the following code inside the `Rational` class declaration:
```cpp
friend std::ostream &operator<<(std::ostream &str, Rational &rhs);
```

---
#### Assignment
Add `>>` operator overload to `Rational` class. It should allow pulling the fraction from the `istream` type stream (for example from the console using `cin`).
Two possible input formats are:
* integral numbers, for example: `5`
* fraction, for example: `7/22`

---

Type conversions
-----------------------------------

In many cases the compiler that won't be able to find the function that exactly matches the set of passed parameters will perform *implicit conversion* to the matching type. Because of that it is possible to execute the code:

```cpp
Rational p = quarter + 1;
```

Compiler will try to perform the calculation using the variable of `Rational` type, because this is the type of the first operand. We implemented `Rational + Rational` operation so only the conversion from integral number to `Rational` is needed. We implemented that as well as a side effect of `Rational(int num, int den)` constructor. The code above will *implicitly* perform the same calculation as written below:

```cpp
Rational p = quarter + Rational(1);
```

The reverse operation (`1 + quarter`) won't work, because the compiler will we looking for the operator that works on `int` type.

We can implement functions that perform conversions *from* different type to our class type in the form of a constructor:

```cpp
Rational(double r); // the constructor converting from double
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
and *to* other type in the form of operator with target type symbol:

```cpp
operator double(); // operator converting to double
```

```cpp
Rational::operator double(){
    return (double)num_ / (double)den_;
}
```

After adding the conversions above it is even possible to execute the following code, despite the fact that we didn't define `/` operator for the `Rational` class.

```cpp
Rational p = one_third / quarter;
```
Why the code above works? Is the result correct?

[//]: # (Skipped: explicit)

Assignments
-----------

#### 1. `Time` class

Design and implement a class that will allow to store the time (duration) and basic operations (addition and subtraction, multiplying by scalar)

The class should allow displaying the value of time in the console, in user-readable format and reading the time from user. Additionally, it should allow conversions to and from seconds.

Example use:

```cpp
Time t1(200);
cout << t1 << endl; // displays 03m:20s
Time t2;
cin >> t2; // user enters 10h:12m:01s

Time t3 = t2 - t1; // 10h:8m:41s
int t3s = t3; // 36521
```

#### 2. *SuperInvoice* program

Write a program that will allow generation invoices in the console. Split the functionality into classes:

`Invoice` - class the describes the invoice, includes the following attributes:
* NIP number (10 digits) of seller and buyer
* the set of bought items

The invoice in its' simplest form should allow adding elements and "printing" of all information.

`Item` - class describing single item from the invoice, includes the following attributes:
* name
* unit net price
* VAT tax type ("A" – 23%, "B" – 8%, "C" – 0%)
* the amount sold

The example invoice after "printing" can look following:
```
------------------VAT invoice------------------
===============================================
Seller: 7770003699      Buyer: 0123456789

                  c.j. VAT   il.    net   total
1. M3 screw     | 0.37  A | 100 | 37.00 | 45.51
2. 2 mm drill   | 2.54  B |   2 |  5.08 |  5.49

------------------------------------ TOTAL ----
                                  42.08 | 51.00
```

Think about what functionality and what interface should every class have. The printing should happen using stream operator both in `Item` and `Invoice` class.

The classes should allow running the code below:

```cpp
Invoice inv(7770003699, 0123456789);
inv.add_item(Item("M3 screw", 0.37, 'A', 100));
inv.add_item(Item("2 mm drill", 2.54, 'B', 2));
std::cout << inv << std::endl;
```

**Bonus:**

Implement addition operator for invoices (check if the NIP numbers are the same and merge items from both invoices). Think about how to store the positions inside the invoice to find the matching elements easily.

***
Authors: *Jakub Tomczyński*, *Michał Fularz*, *Rafał Kabaciński*, *Piotr Kaczmarek*, *Michał Nowicki*, *Jan Wietrzykowski*, *Dominik Pieczyński*
