# Introduction
## Who the hell am I and what the hecky heck is this?
###### This section was last checked in the 1.0.0. version of the engine
I am a highschool student, who is interested in computer science and more specifically, creating video games. This project was a challange I gave myself. The aim was to find out if I am able to create something rather complex with my basic understanding of programming, whilst not looking up solutions to my problems. I have already tried my hands at [a project similar to this](https://github.com/mmmuscus/Snake), but this was way more abitious so it was also a test in planning and creating coexsiting systems.

## So what is the project?
###### This section was last checked in the 1.0.0. version of the engine
The bare minimum I wanted to accomplish was a real time 2D top down game, with a "realistic" binary shading system (this was all planned to be running in the windows console). The concept of the shading system is the following: There is a player (represented by the '@' character); who has a field of view in which he can see things; anything that would block his view (e.g.: an 'x' character) casts a shadow, meaning that he can't see the things behind the "wall", even if those things would be in his field of view.

Right now this shading system has a small bug, which I will discuss in a later section of the documentation. Besides the minimum goal I created basic text based level and field of view editors. I also created the option to make new characters that could block light, or player movement.

Features as of now:
* Real time 8 directional player movement
* Binary shading
* Map and field of view editor
* Character attribute editors (can this character block light? can it block player movement?)

The next main goal of the project is to make an animation pipeline. The plan for it is to have every frame drawn in a textfile under each other, with a general function that can read any animation text file into a 3 dimensional array, and another general function that could play said animation in a set position on the screen. Beside this main goal I plan to make several quality of life changes to the different text based editors.
