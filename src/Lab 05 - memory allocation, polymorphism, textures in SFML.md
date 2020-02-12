Memory allocation, polymorphism, textures in SFML
=====================

Pointers
---------
Variables that we have created through a simple variable declaration had their *life* limited to one *scope* - a fragment of code, most often separated by braces, e.g. the body of a function or the interior of a loop. These variables were created in the memory section called *stack*. Variables and such objects are automatically deleted when they leave the *scope* where they were allocated, e.g. when they leave a function. In most cases, this is desirable and relieves the programmer of the need for manual memory management.

However, there are cases where it is advisable to separate the lifetime of an object from the scope in which it was created. For this purpose, we use the allocation on the *heap*.

Historically, in order to allocate variables on the heap, the operator `new` was used. The variable created in this way had to be removed with the `delete` operator. Since dealocation operations are easy to forget, many programs still have a problem called **memory leaks**.

---
##### Assignment
Perform the following code and observe the use of memory in the *Task Manager* (Ctrl + Alt + Delete):
```cpp
for (int i = 0; i < 10000000000; i++) {
    int *number = new int;
}
std::getchar();
```

---

In order to avoid memory leaks, C++11 introduced new types of so-called *smart pointers*, including `std::unique_ptr` and `std::shared_ptr`. In this instruction, we will omit `std::shared_ptr`, because it is used to store objects that have multiple owners (for example, they are simultaneously in more than one vector). `std::unique_ptr` cannot be copied - an object to which it points has only one owner.

`std::unique_ptr` is a class in which the constructor allocates an object with `new`, and the destructor destroys it with `delete`. By replacing the use of ordinary C-style pointers with `std::unique_ptr` you can easily prevent memory leaks. A variable of the `std::unique_ptr` type will call `delete` on the stored pointer when it goes out of the scope in which it was declared.

---
#### Assignment
Change the previous code so that instead of the `new` operator it will use the `std::unique_ptr` type. Use the [`std::make_unique`](https://en.cppreference.com/w/cpp/memory/unique_ptr/make_unique) function to create `std::unique_ptr`. Observe memory usage.

**Tip:** as we pass the type of variable we want to create on the heap to the `std::make_unique` function, we can **optionally** use the keyword `auto` to bypass declaring an object with a long type name: `std::unique_ptr<int>`. This will not result in a loss of readability of the code.

---

After reading the description above and completing the task, you may get the impression that the allocation on the stack and the allocation on the heap using `std::unique_ptr` are functionally identical. In both cases, the object is destroyed after leaving the range in which it was declared. So why use `std::unique_ptr`?

In C++11, in addition to the type `std::unique_ptr`, there is also a *move semantics* - the ability to "transfer" the object ownership to another scope or container. While in the case of objects declared on the stack, this transfer will copy at least some of the fields of the transferred object, in the case of `std::unique_ptr`, the transfer will not copy the object on the heap.

---
#### Assignment
Try to compile the code below:
```cpp
std::vector<std::unique_ptr<int>> some_pointers;
for (int i = 0; i < 10000000000; i++) {
    auto number = std::make_unique<int>();
    some_pointers.emplace_back(number);
}
```

Consider why this program does not work.

Bearing in mind that the object pointed to by `std::unique_ptr` can have only one owner, pass the ownership to the vector by calling [`std::move`](https://en.cppreference.com/w/cpp/utility/move) on the `number` variable.

---

Polymorphism
-----------
Using pointers to the objects from the heap instead of objects on the stack allows you to easily interpret a pointer to a derived class object as a pointer to a base class object.

---
#### Assignment
Using example classes from the previous instruction, try to compile the code below:
```cpp
std::unique_ptr<Vehicle> skoda_superb_as_vehicle = std::make_unique<Car>(
    "Skoda Superb", "Gasoline", 200, true);

std::cout << "Name: " << skoda_superb_as_vehicle->name() << std::endl;
std::cout << "Has ABS: " << skoda_superb_as_vehicle->has_abs() << std::endl;
```

Knowing that we treat our car as a `Vehicle` class object and analyzing the methods that are implemented in this class, consider why this program does not work. Remove the line causing the compilation error.

Note the difference in a way we are accessing object fields (`->` instead of `.`). Why is that?

---

For the base class to be polymorphic it must contain at least one virtual method (`virtual` keyword). Virtual methods allow to overload them in the derived class in such a way that when we call them through the pointer to the base class, the proper implementation of the derived class will be called. If the function did not have the keyword `virtual`, calling it through the pointer to the base class would call the implementation from the base class, regardless of the fact that there is an overloaded version of the function in the derived class.

It is very important that **every base polymorphic class** contains a declaration of **virtual destructor**. If such a declaration is not present in the base class, deleting the derived class object through the pointer to the base class is **an undefined behavior** and may lead to memory leak, as **only** the base class destructor will be called. The simplest declaration of a virtual destructor is as follows:
```cpp
virtual ~ClassName() = default;
```
In order to reinterpret the pointer to the base class as a pointer or a reference to a derived class, you should dereference the pointer ("pull" the object the pointer points to) and use a special `dynamic_cast` function.

---
#### Assignment
Add virtual destructor to the `Vehicle` class. Analyze and execute the code below:
```cpp
std::vector<std::unique_ptr<Vehicle>> vehicles;
vehicles.emplace_back(std::make_unique<Car>("Skoda Superb", "Gasoline", 200, true));
vehicles.emplace_back(std::make_unique<Bike>());

for (int i = 0; i < vehicles.size(); i++) {
    Car &some_car = dynamic_cast<Car &>(*vehicles[i]);
}
```

What is the reason for the exception being thrown?

We can solve that issue by catching the exception using `try...catch` or we can cast using pointers instead of references and check if the pointer is not empty:

```cpp
for (int i = 0; i < vehicles.size(); i++) {
    Car *some_car = dynamic_cast<Car *>(vehicles[i].get());
    if (some_car != nullptr) { // cast successful
        std::cout << i << ": abs=" << some_car->has_abs() << std::endl;
    } else { // nope
        std::cout << i << ": not a Car" << std::endl;
    }
}
```

**Note:** note that in this case, due to the direct transfer of the result of the function `std::make_unique` to the `emplace_back` method (without using the variable to which we assign the value returned by `std::make_unique`), it was not necessary to use `std::move`.

---

Polymorphism in SFML
--------------------
All "drawable" classes from the SFML library inherit the polymorphic class `sf::Drawable`. This means that we can store all scene objects in one container as `std::unique_ptr<sf::Drawable>`.

---
#### Assignment
Using the theory above, create a common vector for several objects of different shapes and colors in the SFML project.

In the main loop of the program, call the draw method on the `sf:Drawable` objects dereferenced from the pointer.

---

Textures/sprites in SFML
------------------------
Texturing is the application of a flat, two-dimensional image to the geometric shapes in order to give the object a look closer to the real one.

In the case of two-dimensional graphics, the term sprite is used - a flat bitmap that is rendered in whole or in part at a specific point on the screen.

---

#### How to obtain textures?
There are many websites with free multimedia resources such as textures, sprites and sounds - especially for non-commercial use. Databases usually have a well organised directory, which makes it easy to find the right resource.

**Remember** - if you are using graphics downloaded from the Internet, check the license under which resources have been made available. Often, the authors expect only the so-called *attribution* - small mention in program information, where the resources come from and who created them.

The links to a few textures that you can start with are: [grass](./_resources/grass.png), [wall](./_resources/wall.png), [guy](./_resources/guy.png).

---

In SFML, *texture* is the image that is stored in memory, while *sprite* is the shape that will be displayed on the screen and that can be associated with the texture.

An additional description of the sprites can be found [here](https://www.sfml-dev.org/tutorials/2.5/graphics-sprite.php)

The simplest way to use texture is to load a bitmap from a file into the `sf::Texture` object, then create the `sf::Sprite` object and associate it with the loaded texture.

```cpp
sf::Texture texture;
if (!texture.loadFromFile("grass.png")) {
    std::cerr << "Could not load texture" << std::endl;
    return 1;
}

sf::Sprite sprite;
sprite.setTexture(texture);
```

`sf::Sprite` class inherits from `sf::Drawable` and `sf::Transformable` classes, so it contains methods that allow geometric transformations (shifting, rotating, scaling). Moreover, thanks to polymorphism sprites can be stored in one container with shapes such as `sf::RectangleShape`.

---
#### Assignment

Load the supplied textures into the program.

Create one sprite for each of them, display them on the screen in different places and with different scales.

---

If we want the sprite to display only a fragment of the texture associated with it, we can use the `setTextureRect(sf::Rect)` method, which will allow us to point to the selected area. If we choose to display an area larger than the texture size and additionally set the texture to *Repeated* (method `sf::Texture::setRepeated(bool)`, we get *tiling*, i.e. the texture will be repeated to fill the whole area.

---
#### Assignment

Use tiling and *grass* texture to create a background in the program. Determine the appropriate `TextureRect` for the sprite representing the background so that it covers the entire window size.

---

**Important:** Linking a texture to a sprite does not copy its contents - if the `sf::Texture` object is removed and we try to display the sprite to which it was linked, a white fill will appear in place of the texture.

It's also a good idea to keep the number of textures and operations on them to a minimum - for example, use the same texture for multiple objects. For simple games that don't have many resources, it's best to load all the necessary textures at the start of the program and keep them in memory until the end of the program.

Final assignment
----------------
#### 1. Labyrinth

Use the supplied textures to build a *Labyrinth* game.

* Set the background of the game
* Add several objects to the scene - walls with appropriate texture.
* Add a character that you can move with the cursor keys.
* Add collision detection so that it is impossible to pass through walls.

The final effect should be similar to that in [the film](./_resources/labyrinth.mov).


***
Authors: *Dominik Pieczyński*, *Jakub Tomczyński*
