# Animations, platformer game

## Animations

Animations in sprite-based games are based on a set of consecutive animation frames that are switched at the right moments.

The subsequent frames of animation are often placed in one texture and the selection of the frame is made by selecting the appropriate fragment of texture (reminder: `setTextureRect(sf::IntRect)` method). Thanks to this the entire animation can be stored in the memory of the graphic card and the transition to the next frame of animation is computationally inexpensive.

---

#### 🛠🔥 Assignment 🔥🛠

Write a `AnimatedSprite` class inheriting the `sf::Sprite`.

The constructor of the `AnimateSprite` class should accept the animation frame-rate (*fps* - frames per second) and a path to the graphic file containing an animation texture. Moreover, the class interface should allow to set texture fragments that are subsequent frames of animation:

```cpp
AnimatedSprite hero(10, "Character\\character.png");

//hero.add_animation_frame(sf::IntRect(0, 0, 50, 37)); // hero standing frame 1
//hero.add_animation_frame(sf::IntRect(50, 0, 50, 37)); // hero standing frame 2
//hero.add_animation_frame(sf::IntRect(100, 0, 50, 37)); // hero standing frame 3
hero.add_animation_frame(sf::IntRect(150, 0, 50, 37)); // hero running frame 1
hero.add_animation_frame(sf::IntRect(200, 0, 50, 37)); // hero running frame 1
hero.add_animation_frame(sf::IntRect(250, 0, 50, 37)); // hero running frame 1
hero.add_animation_frame(sf::IntRect(300, 0, 50, 37)); // hero running frame 1
hero.add_animation_frame(sf::IntRect(350, 0, 50, 37)); // hero running frame 1
hero.add_animation_frame(sf::IntRect(400, 0, 50, 37)); // hero running frame 1
```

Similarly to the previous classes, it should contain a `animation(sf::Time &elapsed)` method, thanks to which it will be possible to inform the object about the elapsed time. The class, based on this time and the internal parameter describing the number of frames per second, should decide whether to switch to the next frame of the animation.

Create an `AnimatedSprite` object on the scene, using the texture of the character from the [included set of textures](./_resources/sprites.zip).

---

## Gravity

Gravity is a constant acceleration acting in a vertical direction. As in any *time moment*, the current *speed* influences the location of an object, so the current *acceleration* affects *speed*.

---

#### 🛠🔥 Assignment 🔥🛠

Add information about the float vertical speed of the object to the `AnimatedSprite` class, analogous to the `CustomRectangleShape` class. Add a constant float value describing an acceleration (gravity). Include appropriate calculation into the `animate` method causing the object to accelerate towards ground (*fall*).

---

## Final assignments 🔥🛠

#### The game - the "platformer".

Based on the collision detection code in a task from previous classes, write a simple platform game engine:

* add a few objects to the scene - platforms with the appropriate texture,
* add a character - `AnimatedSprite`, which you will be able to move with left and right cursor keys, set gravity for it,
* add collision detection code that will handle collisions between the character and any of the platforms so that the character can stand on the platforms,
* add jumping - if your character is touching the ground/platforms, set his initial speed upwards.

---
Authors: *Jakub Tomczyński*, *Tomasz Mańkowski*
