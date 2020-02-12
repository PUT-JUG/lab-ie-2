OpenGL
=============

OpenGL and GLU in SFML
-------------------------------------------
OpenGL is an open, low-level API for generating 3D graphics. Since the SFML library uses OpenGL internally to draw 2D graphics, we can use it as an helper library to facilitate, for example, the creation of a window and support for input events.

OpenGL provides functions such as drawing single polygons or series of polygons and their transformations. Slightly more high-level functions are part of the GLU library (GL Utility Library).

Since we will use OpenGL and GLU functions directly, it is necessary to include them in the list of libraries for the linker parameters. In the case of the Qt Creator environment, we add the following line to the `.pro` file:

```
LIBS += -lOpenGL32 -lglu32
```

### First OpenGL + SFML program

Create a project to work with SFML and OpenGL. Place the `main.cpp` file in the project with the content taken from the [sfml_hello.cpp](./_resources/sfml_opengl.cpp).

Start the program. A rotating color cube should appear on the screen.

The code structure of the program is analogous to previous SFML programs - the environment is initialized (including OpenGL parameters, such as lighting model), and then the main loop draws frames of the image.

In three-dimensional computer graphics, the most frequently used is the representation of solids in the form of a polygon grid - each object is made up of flat polygons (usually triangles or quadrangles), which have common vertices and edges. This allows you to easily create simple solids, as well as approximate complex objects with a sufficiently dense grid.

The attached cube drawing function draws each of its walls as a square in a different color.

To ensure constant proportions of the displayed image and vertical visibility range, after each `sf::Event::Resized` *event*, the `set_viewport` function is called. This function uses the information about new window size and sets the range of the displayed scene (`glFrustrum`) and the camera position (`glLookAt`) respectively.

---
#### Assignment
Analyze the behavior of the sample code. Check the operation of the `glTranslated`, `glRotated` and `glColor3d` functions by modifying their parameters. Try to deduce how the axes of the scene's coordinate system are arranged.

---

### Geometric transformations in OpenGL

OpenGL applications use a coordinate system to locate objects in a scene. To avoid having to manually recalculate the positions of vertices of objects on the scene, the coordinate system can be transformed (e.g. moved and rotated):
* `glTranslated(double x, double y, double z)` - moves the coordinate system by the distances given as arguments,
* `glRotated(double angle, double x, double y, double z)` - rotates the coordinate system by the `angle` (in degrees) around the axis defined by the parameters `x`, `y`, `z`.

Remember that the order is important. Consider the following examples:

* first the translation (shift) and then the rotation operation was performed:

![Translation then rotation](./_images/lab_07_translation_rotation.png)

* in the second example, the same transformations were carried out, but in the reverse order, first the rotation and then the translation.

![Rotation then translation](./_images/lab_07_rotation_translation.png)

As a result, performing the same operations (but in a different order) gives a different effect - compare the position and orientation of the airplane in both cases.

In order to facilitate the return to the starting point (e.g. after a series of transformations), the following functions have been made available:
* `glPushMatrix()` - saves the current state (position and orientation) of the coordinate system,
* `glPopMatrix()` - retrieves the last saved state (position and orientation) of the coordinate system.
Note that for each `glPushMatrix()` call, one `glPopMatrix()` must be called. They can be treated as braces that enclose a fragment of the code related to the transformation.

Consider the example shown in the figure below:

![Rotation then translation](./_images/lab_07_push_pop.png)

We plan to draw two airplanes - in point *(4, 4)* and then *(2, 1)*. Without using the `glPushMatrix()` / `glPopMatrix()` functions (operations shown above the image) it is necessary to calculate how to move from *(4, 4)* to *(2, 1)*. In this example this seems easy, but there may be more transformations, including rotations that complicate the task.
Using the `glPushMatrix()` / `glPushMatrix()` functions (operations shown below the image), the task is less complicated - no reverse transformations are required - it is enough to remember the current state first and then return to it.

---
#### Assignment
Add two additional cubes of different sizes to your scene. Draw each cube in a different location on the scene and rotate it around a different axis. Use the `glPushMatrix()` / `glPopMatrix()` function.

---

In addition to moving and rotating the coordinate system, it can also be scaled (`glScaled()`) function).

---
#### Assignment
Using the event loop add to the program the ability to move the camera horizontally with *WSAD* keys and zoom in/out with the mouse wheel (event `sf::Event::MouseWheelScrolled` and field `event.mouseWheelScroll.delta`).

**Hint:** Instead of moving the camera, you can modify (move and scale) the coordinate system in which all objects are drawn.

---


Assignment
-----------
#### 1. Planetarium

Write a program showing the motion of planets in the Solar System.

Write a `CelestialBody` class representing the celestial body - to see the rotational motion of planets better, you can represent them as cubes.

Features of the celestial body:

* a diameter
* a distance from the sun
* a period of rotary motion
* a period of circular motion
* a color of the surface

The class should have a `step(float time)` method (similar to the programs from previous classes) and a `draw()` method that causes the drawing of the planet.

Think about the order of the transformations needed to draw the planet in the right place and with the right rotation angle (hint: you don't need trigonometry!).

Load the [solar_system.txt](./_resources/solar_system.txt) file and use it to create objects on the scene representing all planets and the sun. **Note:** the distances and diameters in the file are modified so that the visualization remains readable. Real values can be found in the [solar_system_real.txt](./_resources/solar_system_real.txt) file.


***
Authors: *Jakub Tomczyński*, *Tomasz Mańkowski*
