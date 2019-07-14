# 1. Introduction
###### This section was last checked in the 1.0.0. version of the engine
**Note:** I was using [Dev C++](https://en.wikipedia.org/wiki/Dev-C%2B%2B) as the IDE with TMD-GCC 4.9.2. as the compiler, I have seen the project not functioning as intended with other IDEs and compilers, but it's most probably gonna be fine.
## 1.1. The structure of this documentation
###### This section was last checked in the 1.0.0. version of the engine
This documentation will have three main parts and a [table of contents](#4-table-of-contents):
* [The first](#1-introduction) is the introduction (the part you are reading right now)
* [The second](#2-how-to-use-the-engine) is an overview of [how the engine works](#21-how-the-engine-works-a-breakdown-of-the-main-cpp-file) and [how you can operate it and it's different editors](#22-how-to-use-the-editors-and-other-further-details)
* [The third](#3-detailed-description-of-everything) will go over every last detail about the [defines](#31-defines), [structures](#32-structures), [variables](#33-variables-in-the-main-cpp-file) and [functions](#34-functions) the engine uses

In the [introduction](#1-introduction) I will cover [my motivation](#12-who-the-hell-am-i-and-what-the-hecky-heck-is-this) for this engine, [the idea, the end product and plans about the future of the project](#13-so-what-is-the-project).

[The second part](#2-how-to-use-the-engine) will have two segments. [The first](#21-how-the-engine-works-a-breakdown-of-the-main-cpp-file) detailing the architecture of the system, what it does. [The second](#22-how-to-use-the-editors-and-other-further-details) will be the guide to operate it. Hopefully these two segments will provide you with sufficent information to make a game with this engine.

[The last part](#3-detailed-description-of-everything) will (as mentioned above) go over the hows of the systems in place. It will be broken down into different subsections, each dealing with a header or cpp file and it's [defines](#31-defines), [structures](#32-structures), [variables](#33-variables-in-the-main-cpp-file), or [functions](#34-functions). [This part](#3-detailed-description-of-everything) will act more like a cheat sheet than anything, if you wish to conserve your sanity do not read it from start to finish in one sitting.

## 1.2. Who the hell am I and what the hecky heck is this?
###### This section was last checked in the 1.0.0. version of the engine
I am a highschool student, who is interested in computer science and more specifically, creating video games. This project was a challange I gave myself. The aim was to find out if I am able to create something rather complex with my basic understanding of programming, whilst not looking up solutions to my problems. I have already tried my hands at [a project similar to this](https://github.com/mmmuscus/Snake), but this was way more abitious so it was also a test in planning and creating coexsiting systems.

## 1.3. So what is the project?
###### This section was last checked in the 1.0.0. version of the engine
The bare minimum I wanted to accomplish was a real time 2D top down game, with a "realistic" binary shading system (this was all planned to be running in the windows console). The concept of the shading system is the following: There is a player (represented by the '@' character); who has a field of view in which he can see things; anything that would block his view (e.g.: an 'x' character) casts a shadow, meaning that he can't see the things behind the "wall", even if those things would be in his field of view.

Here is an example for the shading system in action:

![example](https://github.com/mmmuscus/Shadow-Functions-Engine/blob/master/screenShots/example.png)

Right now this system has a small bug, which I will discuss in [a later section of the documentation](#34512-shadowfunction). Besides the minimum goal I created basic text file based level and field of view editors. I also created the option to make new characters that could block light, or player movement.

Features as of now:
* Real time 8 directional player movement
* Binary shading
* Map and field of view editors
* Character attribute editors (can this character block light? can it block player movement?)

The next main goal of the project is to make an animation pipeline. The plan for it is to have every frame drawn in a textfile under each other, with a general function that can read any animation text file into a 3 dimensional array, and another general function that could play said animation in a set position on the screen. Beside this main goal I plan to make several quality of life changes to the different text based editors and to the structure and efficency of the code in general.




### 3.4.4. [render.h](https://github.com/mmmuscus/Shadow-Functions-Engine/blob/master/headers/rendering/render.h)
###### This section was last checked in the 1.0.0. version of the engine
This header contains functions that either help rendering, or actually render stuff.
#### 3.4.4.1. goTo
###### This section was last checked in the 1.0.0. version of the engine
```cpp
void goTo (int row, int column)
{
	HANDLE hStdout;
	hStdout = GetStdHandle(STD_OUTPUT_HANDLE);
	
	COORD destCoord;
	
	destCoord.X = column;
	destCoord.Y = row;
	
	SetConsoleCursorPosition(hStdout, destCoord);
}
```
**Usage:** This function puts the cursor into a set position in the console window.

**Variables:**
* **row:** The row of the cell to which the cursor will be put on the console window.
* **col:** The column of the cell to which the cursor will be put on the console window.

**How it's done & notes:** This was one of the three times where I resorted to outside sources to solve my problem. I found my answer [here](https://stackoverflow.com/questions/10401724/move-text-cursor-to-particular-screen-coordinate) in the fourth answer. For information about the coordinate system in place please click [here](#2232-further-ramblings-about-the-coordinate-system).
#### 3.4.4.2. clearScreen
###### This section was last checked in the 1.0.0. version of the engine
```cpp
void clearScreen()
{
	HANDLE hConsole;
	hConsole = GetStdHandle(STD_OUTPUT_HANDLE);	

	COORD coordScreen = { 0, 0 };
	DWORD cCharsWritten;
	CONSOLE_SCREEN_BUFFER_INFO csbi; 
	DWORD dwConSize;
	
	// Get the number of character cells in the current buffer. 

	if( !GetConsoleScreenBufferInfo( hConsole, &csbi ))
		return;
	dwConSize = csbi.dwSize.X * csbi.dwSize.Y;

	// Fill the entire screen with blanks.

	if( !FillConsoleOutputCharacter( hConsole, (TCHAR) ' ',
    	dwConSize, coordScreen, &cCharsWritten ))
    	return;

	// Get the current text attribute.

	if( !GetConsoleScreenBufferInfo( hConsole, &csbi ))
    	return;

	// Set the buffer's attributes accordingly.

	if( !FillConsoleOutputAttribute( hConsole, csbi.wAttributes,
    	dwConSize, coordScreen, &cCharsWritten ))
    	return;

	// Put the cursor at its home coordinates.

	SetConsoleCursorPosition( hConsole, coordScreen );
}
```
**Usage:** Clears the console window. 

**Variables:** -

**How it's done & notes:** This was one of the three times where I resorted to outside sources to solve my problem. Sadly I didn't keep the original link, but I found this implementation of a clear screen function right [here](https://docs.microsoft.com/en-us/windows/console/clearing-the-screen)!
#### 3.4.4.3. renderScreen
###### This section was last checked in the 1.0.0. version of the engine
```cpp
void renderScreen(char oldS[SCREENROWS][SCREENCOLS], char newS[SCREENROWS][SCREENCOLS])
{
	for (int i = 0; i < SCREENROWS; i++)
	{
		for (int j = 0; j < SCREENCOLS; j++)
		{
			if (newS[i][j] != oldS[i][j])
			{
				goTo(i, j);
				cout<<newS[i][j];
			}
		}
	}
	
	goTo(SCREENROWS, 0);
}
```
**Usage:** This renders the "screen" part of the console window. 

**Variables:**
* **oldS:** This array holds the textures from the last frame of the "screen" part of the console window.
* **newS:** This array holds the textures from this frame of the "screen" part of the console window.

**How it's done & notes:** The function loops through both of the arrays and compares each cell's textures. If there is a difference, it goes to the correct cell of the console window with [the goTo function](#3441-goto), and updates the cell with the correct texture. This means that the engine only updates cells that's texture changed in this frame, if the engine would update every cell every frame that would lead to flashing, sometimes not even completing the updates properly and (obviously) a much slower runtime.
#### 3.4.4.4. renderMenu
###### This section was last checked in the 1.0.0. version of the engine
```cpp
void renderMenu(char oldM[SCREENROWS][MENUCOLS], char newM[SCREENROWS][MENUCOLS])
{
	for (int i = 0; i < SCREENROWS; i++)
	{
		for (int j = 0; j < MENUCOLS; j++)
		{
			if (newM[i][j] != oldM[i][j])
			{
				goTo(i, SCREENCOLS + j);
				cout<<newM[i][j];
			}
		}
	}
}
```
**Usage:** This renders the "menu" part of the console window.

**Variables:**
* **oldM:** This array holds the textures from the last frame of the "menu" part of the console window.
* **newM:** This array holds the textures from this frame of the "menu" part of the console window.

**How it's done & notes:** The function loops through both of the arrays and compares each cell's textures. If there is a difference, it goes to the correct cell of the console window with [the goTo function](#3441-goto), and updates the cell with the correct texture. This means that the engine only updates cells that's texture changed in this frame, if the engine would update every cell every frame that would lead to flashing, sometimes not even completing the updates properly and (obviously) a much slower runtime.
#### 3.4.4.5. calculateScreen
###### This section was last checked in the 1.0.0. version of the engine
```cpp
void calculateScreen(map world[WORLDROWS][WORLDCOLS], char screen[SCREENROWS][SCREENCOLS], int cameraRow, int cameraCol)
{
	for (int i = 0; i < SCREENROWS; i++)
	{
		for (int j = 0; j < SCREENCOLS; j++)
		{
			if (world[i + cameraRow][j + cameraCol].mapInView)
			{
				screen[i][j] = world[i + cameraRow][j + cameraCol].texture;
			}
			else
			{
				if (world[i + cameraRow][j + cameraCol].mapIsEdge)
				{
						screen[i][j] = char(176);
				}
				else
				{
					screen[i][j] = char(178);
				}
			}
		}
	}
}
```
**Usage:** This function translates all of the information calculated by the [shadowFunction](#34512-shadowfunction) and [mapIsEdgeCalculation](#34514-mapisedgecalculation) functions into textures that can be rendered.

**Variables:**
* **world:** This array holds all of the information about the cells of the world, like what is or isn't visible, etc.
* **screen:** This array will hold the textures of the "screen" part of the console window after the function is finished.
* **cameraRow:** This is the value that holds in which row the camera is currently.
* **cameraCol:** This is the value that holds in which column the camera is currently.

**How it's done & notes:** The function loops through a part of the map that is equal in size to the "screen" part of the console window. Then if the cell of the world is not in view the texture of the cell on the screen becomes '▓'. If the cell of the world is in the edge, the texture of the cell on the screen becomes'░', and if neither of those are true the texture of the cell on the screen becomes the texture of the cell on the world. For mor information about the map structure click [here](#322-map).
### 3.4.5. [shadowFunctions.h](https://github.com/mmmuscus/Shadow-Functions-Engine/blob/master/headers/rendering/shadowFunctions.h)
###### This section was last checked in the 1.0.0. version of the engine
This header contains functions that prepare for the shading of the correct parts of the game world, and also [the function that actually shades the correct places](#34512-shadowfunction).
#### 3.4.5.1. makeCurrentFov
###### This section was last checked in the 1.0.0. version of the engine
```cpp
void makeCurrentFov(fov presetDir[FOVROWS][FOVCOLS], fov toBeDir[FOVROWS][FOVCOLS])
{
	for (int i = 0; i < FOVROWS; i++)
	{
		for (int j = 0; j < FOVCOLS; j++)
		{
			toBeDir[i][j].inView = presetDir[i][j].inView;
			toBeDir[i][j].isPlayer = presetDir[i][j].isPlayer;
		}
	}
}
```
**Usage:** This function copies a [FOV array](#3317-fov-arrays) into another [FOV array](#3317-fov-arrays).

**Variables:**
* **presetDir:** This is the [FOV array](#3317-fov-arrays) that gets copied.
* **toBeDir:** This is the [FOV array](#3317-fov-arrays) that gets overwritten.

**How it's done & notes:** The function loops through each variable of the arrays, and copies the different sub variables from one to the other. For more information about the fov structure click [here](#323-fov).
#### 3.4.5.2. setCurrentFov
###### This section was last checked in the 1.0.0. version of the engine
```cpp
void setCurrentFov(mob playr, fov toBecomeCurrentFov[FOVROWS][FOVCOLS], fov r[FOVROWS][FOVCOLS], fov l[FOVROWS][FOVCOLS], fov u[FOVROWS][FOVCOLS], fov d[FOVROWS][FOVCOLS], fov ru[FOVROWS][FOVCOLS], fov rd[FOVROWS][FOVCOLS], fov lu[FOVROWS][FOVCOLS], fov ld[FOVROWS][FOVCOLS])
{
	if (playr.right && !playr.up && !playr.down)
	{
		makeCurrentFov(r, toBecomeCurrentFov);
	}
	
	if (playr.right && playr.up)
	{
		makeCurrentFov(ru, toBecomeCurrentFov);
	}
	
	if (playr.right && playr.down)
	{
		makeCurrentFov(rd, toBecomeCurrentFov);
	}
	
	if (playr.left && !playr.up && !playr.down)
	{
		makeCurrentFov(l, toBecomeCurrentFov);
	}
	
	if (playr.left && playr.up)
	{
		makeCurrentFov(lu, toBecomeCurrentFov);
	}
	
	if (playr.left && playr.down)
	{
		makeCurrentFov(ld, toBecomeCurrentFov);
	}
	
	if (playr.up && !playr.right && !playr.left)
	{
		makeCurrentFov(u, toBecomeCurrentFov);
	}
	
	if (playr.down && !playr.right && !playr.left)
	{
		makeCurrentFov(d, toBecomeCurrentFov);
	}
}
```
**Usage:** This function sets the current [FOV array](#3317-fov-arrays) to the correct [FOV array](#3317-fov-arrays) according to the input.

**Variables:**
* **playr:** This variable holds the needed information about the input.
* **toBecomeCurrentFov:** This array will contain the correct [FOV array](#3317-fov-arrays) according to this frame's inputs.
* **r:** This array holds the information about how the FOV looks when the player is looking to the right.
* **l:** This array holds the information about how the FOV looks when the player is looking to the left.
* **u:** This array holds the information about how the FOV looks when the player is looking upwards.
* **d:** This array holds the information about how the FOV looks when the player is looking downwards.
* **ru:** This array holds the information about how the FOV looks when the player is looking upwards and to the right.
* **rd:** This array holds the information about how the FOV looks when the player is looking downwards and to the right.
* **lu:** This array holds the information about how the FOV looks when the player is looking upwards and to the left.
* **ld:** This array holds the information about how the FOV looks when the player is looking downwards and to the left.

**How it's done & notes:** The function checks each of the 8 cases one by one and then copies the correct FOV array into the toBecomeCurrentFov array, with the use of [the makeCurrentFov function](#3451-makecurrentfov).
#### 3.4.5.3. getPlayerPosInFov
###### This section was last checked in the 1.0.0. version of the engine
```cpp
mob getPlayerPosInFov(mob playr, mob fovPlayr)
{
	if (playr.right && !playr.up && !playr.down)
	{
		fovPlayr.row = 10;
		fovPlayr.col = 1;
	}
	
	if (playr.right && playr.up)
	{
		fovPlayr.row = 14;
		fovPlayr.col = 7;
	}
	
	if (playr.right && playr.down)
	{
		fovPlayr.row = 6;
		fovPlayr.col = 7;
	}
	
	if (playr.left && !playr.up && !playr.down)
	{
		fovPlayr.row = 10;
		fovPlayr.col = 33;
	}
	
	if (playr.left && playr.up)
	{
		fovPlayr.row = 14;
		fovPlayr.col = 27;
	}
	
	if (playr.left && playr.down)
	{
		fovPlayr.row = 6;
		fovPlayr.col = 27;
	}
	
	if (playr.up && !playr.right && !playr.left)
	{
		fovPlayr.row = 20;
		fovPlayr.col = 17;
	}
	
	if (playr.down && !playr.right && !playr.left)
	{
		fovPlayr.row = 0;
		fovPlayr.col = 17;
	}
	
	return fovPlayr;
}
```
**Usage:** This function sets where the player is in the [FOV array](#3317-fov-arrays) accoding to the orientation of the player, or in other worlds which FOV does the player currently use.

**Variables:**
* **playr:** This variable holds the needed information about the input.
* **fovPlayr:** This variable will hold where the player is in the current [FOV array](#3317-fov-arrays).

**How it's done & notes:** The function checks each of the 8 cases one by one, then hardcodes the correct values into the .col and .row sub variables of the fovPlayr variable. For more information about the mob structure click [here](#321-mob). The information from this fovPlayr variable will be used as an anchor point by [the function that adds the information from the current FOV array into the map](#3454-addfovinfotomap). A better implementation of this function is planned for the 2.0.0. update!
#### 3.4.5.4. addFovInfoToMap
###### This section was last checked in the 1.0.0. version of the engine
```cpp
void addFovInfoToMap(map world[WORLDROWS][WORLDCOLS], mob playr, mob fovPlayr, fov fov[FOVROWS][FOVCOLS])
{
	for (int i = 0; i < WORLDROWS; i++)
	{
		for (int j = 0; j < WORLDCOLS; j++)
		{
			world[i][j].mapIsEdge = false;
			world[i][j].mapInView = false;
		}
	}
	
	for (int i = 0; i < FOVROWS; i++)
	{
		for (int j = 0; j < FOVCOLS; j++)
		{
			if (fov[i][j].inView)
			{
				world[playr.row - fovPlayr.row + i][playr.col - fovPlayr.col + j].mapInView = true;
			}
		}
	}
}
```
**Usage:** This function adds the infromation from [the current FOV array](#3318-currentfov) about the different cells (if they are visible or not) into the map of the world.

**Variables:**
* **world:** This array will hold the information if the cell is in view or not, for all of the cells in the world.
* **playr:** This variable holds the position of the player in the world.
* **fovplayr:** This variable holds the position of the player in [the current FOV array](#3318-currentfov).
* **fov:** This array holds information about the current FOV of the player. 

**How it's done & notes:** First the function sets all of the cells to not visible in [the world array](#3321-newworld). Then we start looping over a chunk of the world which's size equals that of [the FOV ararys](#3317-fov-arrays). If a cell in [the current FOV array](#3318-currentfov) is in view we set the correct cell in the world to in view. We do this with the help of our anchor point. Since the anchor point is the position of the player in [the current FOV array](#3318-currentfov), if we subtract it from the player's position in the world we get the top left point of the FOV of the player in the world. After that we can just simply loop over every cell of [the current FOV array](#3318-currentfov) by adding i and j to the correct dimensions.  
#### 3.4.5.5. getPov
###### This section was last checked in the 1.0.0. version of the engine
```cpp
koordinate getPov(koordinate pov, mob playr)
{
	pov.x = playr.col + 0.5;
	pov.y = playr.row + 0.5;

	return pov;
}
```
**Usage:** This gets the point of view from which the player "sees" things.

**Variables:**
* **pov:** This variable will contain the coordinates of the point from which the player "sees" things.
* **playr:** This variable holds the position of the player in the world.

**How it's done & notes:** We just simply add 0.5 to the position of the player. Since we will cast lines to the edges of different cells in [the function that shades things](#34512-shadowfunction), and since the edges of the cells all have integer cordinates for their four points, adding 0.5 to both of the coordinates of the player prevents the engine from casting lines that are vertical (vertical lines can't be described by the line equation the engine is using, for more information click [here](#324-line)). **Important:** the playr variable has cell coordinates in it and the pov variable will have point coordinates in it, for more information about the difference between the two click [here](#2232-further-ramblings-about-the-coordinate-system).
#### 3.4.5.6. getLineEquation
###### This section was last checked in the 1.0.0. version of the engine
```cpp
line getLineEquation(double aXCol, double aYRow, int bXCol, int bYRow)
{
	line e;

	e.mSlope = (bYRow - aYRow) / (bXCol - aXCol);
	e.bIntercept = aYRow - (e.mSlope * aXCol);

	return e;
}
```
**Usage:** This function determines the line that passes throught two points.

**Variables:**
* **aXCol:** The x coordinate of the first point.
* **aYRow:** The y coordinate of the first point.
* **bXCol:** The x coordinate of the second point.
* **bYRow:** The y coordinate of the second point.

**How it's done & notes:** It can be proven that the operations I'm doing with those variables produce the desired values (for an explanation click [here](https://www.khanacademy.org/math/algebra/two-var-linear-equations/writing-slope-intercept-equations/v/equation-of-a-line-3) and for an explanation of the line formula the engine uses click [here](#324-line)). It should be noted that the second point's coordinates are set as integers, this is because I use this function to cast lines from the player to the edges of objects, and the edges of the objects are always integers (for further information about this click [here](#2232-further-ramblings-about-the-coordinate-system)).
#### 3.4.5.7. isUnderLine
###### This section was last checked in the 1.0.0. version of the engine
```cpp
bool isUnderLine(line e, int solidYRow, int solidXCol)
{
	if ((solidYRow <= (solidXCol * e.mSlope) + e.bIntercept) && ((solidYRow + 1) <= (solidXCol * e.mSlope) + e.bIntercept) && (solidYRow <= ((solidXCol + 1) * e.mSlope) + e.bIntercept) && ((solidYRow + 1) <= ((solidXCol + 1) * e.mSlope) + e.bIntercept))
	{
		return true;
	}

	return false;
}
```
**Usage:** This function returns true if a cell is under a line (this icludes the case when the cell is just touching the line).

**Variables:**
* **e:** The line in question.
* **solidYRow:** The y coordinate of the cell.
* **solidXCol:** The x coordinate of the cell.

**How it's done & notes:** The function checks for all of the four points of the cell if its under the given line, if all four are then it returns true. For the difference between cell and point coordinates click [here](#2232-further-ramblings-about-the-coordinate-system). For more information about the line formula click [here](#324-line).
#### 3.4.5.8. isOverLine
###### This section was last checked in the 1.0.0. version of the engine
```cpp
bool isOverLine(line e, int solidYRow, int solidXCol)
{
	if ((solidYRow >= (solidXCol * e.mSlope) + e.bIntercept) && ((solidYRow + 1) >= (solidXCol * e.mSlope) + e.bIntercept) && (solidYRow >= ((solidXCol + 1) * e.mSlope) + e.bIntercept) && ((solidYRow + 1) >= ((solidXCol + 1) * e.mSlope) + e.bIntercept))
	{
		return true;
	}

	return false;
}
```
**Usage:** This function returns true if a cell is over a line (this icludes the case when the cell is just touching the line).

**Variables:**
* **e:** The line in question.
* **solidYRow:** The y coordinate of the cell.
* **solidXCol:** The x coordinate of the cell.

**How it's done & notes:** The function checks for all of the four points of the cell if its over the given line, if all four are then it returns true. For the difference between cell and point coordinates click [here](#2232-further-ramblings-about-the-coordinate-system). For more information about the line formula click [here](#324-line).
#### 3.4.5.9. isBetweenLines
###### This section was last checked in the 1.0.0. version of the engine
```cpp
bool isBetweenLines(line a, line b, int yRow, int xCol)
{
	if (a.isItUnderLine)
	{
		if (!isUnderLine(a, yRow, xCol))
		{
			return false;
		}
	}
	else
	{
		if (!isOverLine(a, yRow, xCol))
		{
			return false;
		}
	}

	if (b.isItUnderLine)
	{
		if (!isUnderLine(b, yRow, xCol))
		{
			return false;
		}
	}
	else
	{
		if (!isOverLine(b, yRow, xCol))
		{
			return false;
		}
	}

	return true;
}
```
**Usage:** This function returns true if a cell is between two lines (a cell is also determined to be between two lines if any of the lines touch it in just one point).

**Variables:**
* **a:** The first line. 
* **b:** The second line.
* **yRow:** The y coordinate of the cell.
* **xCol:** The x coordinate of the cell.

**How it's done & notes:** The engine checks both of the possibilities (is the line under or over the cell we are checking) for both of the lines. This is done by utilizing properties of the line structure (for more info click [here](#324-line)), [the isUnderline function](#3457-isunderline) and [the isOverLine function](#3458-isoverline).
#### 3.4.5.10. isBehindWall
###### This section was last checked in the 1.0.0. version of the engine
```cpp
bool isBehindWall(koordinate pov, int yRow, int xCol, int top, int bottom, int right, int left)
{
	if (pov.x < left && xCol < left + 1)
	{
		return false;
	}
	
	if (pov.x > right && xCol > right - 2)
	{
		return false;
	}
	
	if (pov.y < top && yRow < top + 1)
	{
		return false;
	}
	
	if (pov.y > bottom && yRow > bottom - 2)
	{
		return false;
	}
	
	return true;
}
```
**Usage:** This function returns true if the cell and the player character are on "opposite sides" of a rectangle.

**Variables:**
* **pov:** This variable holds the point from which the player "sees" things. 
* **yRow:** This variable holds the y coordinate of the cell.
* **xCol:** This variable holds the x coordinate of the cell.
* **top:** This variable holds the y coordinate of the top side of the rectangle.
* **bottom:** This variable holds the y coordinate of the bottom side of the rectangle.
* **right:** This variable holds the x coordinate of the right side of the rectangle.
* **left:** This variable holds the x coordinate of the left side of the rectangle.

**How it's done & notes:** For explaining the workings of this function I will use small figures. In these the '@' character will represent the player character, the 'x' will represent the wall of the rectangels, and the '#' will represent the cells that are deemed to be "behind the wall" by the function (if the 'x' and '#' characters would overlap I will use '0' characters to represent both of the in one cell). The '|' characters are just there to make everything preiiter.
```
1. |   ######| 2. |######   | 3. |   ######| 4. |    @    | 5. |    @    |
   |   ######|    |######   |    |  x######|    |         |    |         |
   |@ x000000|    |000000x @|    |@ x######|    | xxxxxxx |    |    x    |
   |   ######|    |######   |    |  x######|    |#########|    |####0####|
   |   ######|    |######   |    |   ######|    |#########|    |####0####|
```
This function will be used to help determine which cells are behind the ones casting shadows. This is the reasoning behind the lone 'x' character (in the first two and last figures) not being "behind the wall", since that character is the one casting shadows. In the above cases the function would return true or false according to the figures. Basically the function determines whether the cell is behind (from the perspective of the player) the first cell(s) of a rectangle.
```
|@        |
|         |
|   xxx   |
|    #####|
|    #####|
```
The function only checks for the cases when it would need to return false, or in other words when the player and the cell are in on the same sides of the rectangle. If thats not the case it goes on to check all of the other ifs as well. So in the end it will combine all of the ifs and produce a result when the player is not in one line with any of the cells, like the on in the figure above.

Since cell coordinates refer to the top left point of the cell, and the above mentioned reasons the wierd requirements of `xCol > right - 2` and `yRow < top + 1` are required. For further information about the coordinate sytem and the difference between cell and point coordinates click [here](#2232-further-ramblings-about-the-coordinate-system).
#### 3.4.5.11. getEdgeLines
###### This section was last checked in the 1.0.0. version of the engine
```cpp
edgeLines getEdgeLines(koordinate pov, int top, int bot, int right, int left)
{
	edgeLines edg;
	
	if (pov.y < top)
	{
		if (pov.x < left)
		{
			edg.first = getLineEquation(pov.x, pov.y, right, top);
			edg.first.isItUnderLine = false;
			edg.second = getLineEquation(pov.x, pov.y, left, bot);
			edg.second.isItUnderLine = true;
			return edg;
		}
		else if (pov.x > right)
		{
			edg.first = getLineEquation(pov.x, pov.y, left, top);
			edg.first.isItUnderLine = false;
			edg.second = getLineEquation(pov.x, pov.y, right, bot);
			edg.second.isItUnderLine = true;
			return edg;
		}
		else
		{
			edg.first = getLineEquation(pov.x, pov.y, right, top);
			edg.first.isItUnderLine = false;
			edg.second = getLineEquation(pov.x, pov.y, left, top);
			edg.second.isItUnderLine = false;
			return edg;
		}
	}
	else if (pov.y > bot)
	{
		if (pov.x < left)
		{
			edg.first = getLineEquation(pov.x, pov.y, left, top);
			edg.first.isItUnderLine = false;
			edg.second = getLineEquation(pov.x, pov.y, right, bot);
			edg.second.isItUnderLine = true;
			return edg;
		}
		else if (pov.x > right)
		{
			edg.first = getLineEquation(pov.x, pov.y, right, top);
			edg.first.isItUnderLine = false;
			edg.second = getLineEquation(pov.x, pov.y, left, bot);
			edg.second.isItUnderLine = true;
			return edg;
		}
		else
		{
			edg.first = getLineEquation(pov.x, pov.y, right, bot);
			edg.first.isItUnderLine = true;
			edg.second = getLineEquation(pov.x, pov.y, left, bot);
			edg.second.isItUnderLine = true;
			return edg;
		}
	}
	else
	{
		if (pov.x < left)
		{
			edg.first = getLineEquation(pov.x, pov.y, left, top);
			edg.first.isItUnderLine = false;
			edg.second = getLineEquation(pov.x, pov.y, left, bot);
			edg.second.isItUnderLine = true;
			return edg;
		}
		else if (pov.x > right)
		{
			edg.first = getLineEquation(pov.x, pov.y, right, top);
			edg.first.isItUnderLine = false;
			edg.second = getLineEquation(pov.x, pov.y, right, bot);
			edg.second.isItUnderLine = true;
			return edg;
		}
	}
}
```
**Usage:** This function returns the two lines that are cast from the player, both touch the rectangle in just one point, and encapsulate it. 

**Variables:**
* **pov:** This variable holds the point from which the player "sees" things. 
* **top:** This variable holds the y coordinate of the top side of the rectangle.
* **bot:** This variable holds the y coordinate of the bottom side of the rectangle.
* **right:** This variable holds the x coordinate of the right side of the rectangle.
* **left:** This variable holds the x coordinate of the left side of the rectangle.

**How it's done & notes:** The function checks all of the 8 different possibilites one by one and then sets the two line's variables to the correct values using [the getLineEquation function](#3456-getlineequation).
#### 3.4.5.12. shadowFunction
###### This section was last checked in the 1.0.0. version of the engine
```cpp
void shadowFunction(map world[WORLDROWS][WORLDCOLS], int cameraCol, int cameraRow, koordinate pov, edgeLines edg)
{
	for (int i = 0; i < SCREENROWS; i++)
	{
		int j = 0;
			
		while (j < SCREENCOLS)
		{
			if (world[i + cameraRow][j + cameraCol].solid)
			{
				int k = 0;
				
				while (world[i + cameraRow][j + cameraCol + k].solid)
				{
					k++;
				}
				
				edg = getEdgeLines(pov, i + cameraRow, i + cameraRow + 1, j + k + cameraCol, j + cameraCol);
				
				for (int g = 0; g < SCREENROWS; g++)
				{
					for(int h = 0; h < SCREENCOLS; h++)
					{
						if (isBetweenLines(edg.first, edg.second, g + cameraRow, h + cameraCol) && isBehindWall(pov, g + cameraRow, h + cameraCol, i + cameraRow, i + cameraRow + 1, j + k + cameraCol, j + cameraCol))
						{
							world[g + cameraRow][h + cameraCol].mapInView = false;
						}
					}
				}
				
				j += k;
			}
			else
			{
				j++;
			}
		}
	}
	
	for (int i = 0; i < SCREENCOLS; i++)
	{
		int j = 0;
		
		while (j < SCREENROWS)
		{
			if (world[j + cameraRow][i + cameraCol].solid)
			{
				int k = 0;
				
				while (world[j + cameraRow + k][i + cameraCol].solid)
				{
					k++;
				}
				
				edg = getEdgeLines(pov, j + cameraRow, j + k + cameraRow, i + cameraCol + 1, i + cameraCol);
				
				for (int g = 0; g < SCREENROWS; g++)
				{
					for(int h = 0; h < SCREENCOLS; h++)
					{
						if (isBetweenLines(edg.first, edg.second, g + cameraRow, h + cameraCol) && isBehindWall(pov, g + cameraRow, h + cameraCol, j + cameraRow, j + k + cameraRow, i + cameraCol + 1, i + cameraCol))
						{
							world[g + cameraRow][h + cameraCol].mapInView = false;
						}
					}
				}
				
				j += k;
			}
			else
			{
				j++;
			}
		}
	}
}
```
**Usage:** This function determines which cells are in shade in the world.

**Variables:**
* **world:** This array will hold the information about the visibility of the cells.
* **cameraCol:** The column in which the camera is.
* **cameraRow:** The row in which the camera is.
* **pov:** This variable holds the point from which the player "sees" things. 
* **edg:** This variable will be used to hold the lines that are cast from the player to the rectangle.

**How it's done & notes:** The function goes over the screen of the game first horizontally, then vertically searching for rectangles that are 1 by x (or x by 1) in dimension. For the rest of this explanation I will be talking about the horizontal search of the screen, since the vertical search is the same, all the same porcesses happen they are just just flipped by 90 degrees. So, the function starts sweeping the screen horizontally, starting with the first line then going down to the next one by one. If it finds a cell that has the ability to block light it will find the end of that x long rectangle. This will be done with a while function that increments untill the cell it is on dosen't block light. 

Now we have the edges of that rectangle (`i + cameraRow, i + cameraRow + 1, j + k + cameraCol, j + cameraCol`), we will hand this information to [the getEdgeLines function](#34511-getedgelines), which will determine the two lines that will get cast from the player to the rectangle.

Now that we have these lines we start to go over every cell of the screen again, and determine if that cell is between the lines AND if that cell is behind the rectangle (this is done with the help of [the isBetweenLines](#3459-isbetweenlines) and [the isBehindWall](#34510-isbehindwall) functions). If both is true we set that cells visibility to false (all of the cells that are in the FOV of the player are set to visible by [the addFovInfoToMap function](#3454-addfovinfotomap), for more info about the map structure click [here](#322-map)).

![bug](https://github.com/mmmuscus/Shadow-Functions-Engine/blob/master/screenShots/bug.png)

Since this function only shades cells that are FULLY between the two lines, wierd things like the one you see above can happen. This is not a bug, just a flaw in planning. I plan to adress this issue in further updates.
#### 3.4.5.13. isBesideNotSolidInView
###### This section was last checked in the 1.0.0. version of the engine
```cpp
bool isBesideNotSolidInView(map world[WORLDROWS][WORLDCOLS], int xCol, int yRow)
{
	for (int i = yRow - 1; i <= yRow + 1; i++)
	{
		for (int j = xCol - 1; j <= xCol + 1; j++)
		{
			if (world[j][i].mapInView && !world[j][i].solid)
			{
				return true;
			}
		}
	}
	
	return false;
}
```
**Usage:** This function returns true if there are any cells beside this one that are in view and do not block light.

**Variables:**
* **world:** This array holds information about the world of the game.
* **xCol:** This varaible holds the x coordinate of the cell.
* **yRow:** This varaible holds the y coordinate of the cell.

**How it's done & notes:** Checks for all of the 8 cells that are beside this one if these conditions apply. The function also checks for the cell that is given to it whan called, but this is not a problem since we will oly call this function in [the mapIsEdgeCalculation function](#34514-mapisedgecalculation) for cells that are not in view.
#### 3.4.5.14. mapIsEdgeCalculation
###### This section was last checked in the 1.0.0. version of the engine
```cpp
void mapIsEdgeCalculation(map world[WORLDROWS][WORLDCOLS], int cameraRow, int cameraCol)
{
	for (int i = 0; i < SCREENROWS + 2; i++)
	{
		world[cameraRow - 1 + i][cameraCol - 1].mapInView = false;
		world[cameraRow - 1 + i][cameraCol + SCREENCOLS].mapInView = false;
	}
	
	for (int i = 0; i < SCREENCOLS + 2; i++)
	{
		world[cameraRow - 1][cameraCol - 1 + i].mapInView = false;
		world[cameraRow + SCREENROWS][cameraCol - 1 + i].mapInView = false;
	}
	
	for (int i = 0; i < SCREENROWS; i++)
	{
		for (int j = 0; j < SCREENCOLS; j++)
		{
			if (!world[i + cameraRow][j + cameraCol].mapInView)
			{
				if (isBesideNotSolidInView(world, i + cameraRow, j + cameraCol))
				{
					world[i + cameraRow][j + cameraCol].mapIsEdge = true;
				}
			}
		}
	}
}
```
**Usage:** This function calculates where the less shadow-y shadows should be displayed (they should be around everything that is in view).

**Variables:**
* **world:** This array holds information about the world of the game.
* **cameraRow:** This variable holds the row of the camera.
* **cameraCol:** This variable holds the column of the camera.

**How it's done & notes:** First we need to set all of the cells around the screen to not being in view, since if we don't all sorts of funny buisness can happen (less shadow-y shadows appearing at the edge of the screen where they shouldn't be). Then we go over all of the cells that will be displayed and call [the isBesideNotSolidInView function](#34513-isbesidenotsolidinview) with them if they are not in view. If that function returns ture we set the isEdge sub variable of the cell to true (for more information about the map structure click [here](#322-map)).
