# Matthew Fraser Assignment 

Project Idea

For my assignment, I am going to develop a 3D Maze in OpenGL. The maze will be randomly generated each time the player plays the game.
The player will then have to rotate the maze to roll a ball with the aim of getting it to the designated location that is also randomly decided.

Stretch Goals:
- Add a 2 player turn mode
- Add a sandbox mode where the player can pick the size of the maze
- Add enemy balls that the user has to avoid by rotating the maze in certain directions
- Add a dungeon crawler type mode where you keep playing different sized mazes until you die


# Report

Introduction

For my project, I developed a 3D maze game inside of OpenGL; the aim of this game is to tilt the maze to roll the ball towards the end target. The maze is randomly generated allowing me to add variation each time the player plays. I also included multiple game modes to show off the basic principle in different ways. 

![Alt text](Homescreen.png?raw=true "Homescreen")

Initial Research

For preliminary research, I focused on maze generation. After looking at different maze generation algorithms (1)  I settled on using the recursive backtracker algorithms as this was the most straightforward allowing me to implement it faster. The logical recursive loop checking for each cell made this algorithm easy to understand. In comparison others such as Ellers that are faster however are a lot more complex due to the number of steps the algorithm must complete. I also directed some of my research on box and sphere collisions, In the end, I used the closest point algorithm (2). For the Ball wall collision I used the AABB-sphere algorithm, this takes the closest point on the wall and finds the distance to the ball. This simplified the collision allowing me to debug issues quicker. Whereas for the ball, goal/enemies collision I used a sphere sphere detection. This was a simple algorithm of which I understood the maths behind, therefore there was no need for anything more complicated. Finally, I did a lot of investigation into rendering dynamic text and buttons to the screen. I decided to research how to render directly to the screen so the text and buttons were always parented to the screen. For this, I set up my own vertex and fragment shaders. This would be used to pass the vertice data from the buttons and convert to screen space. To render text I used SDL_ttf and OpenGL textures (3). This allowed me to add dynamic text as OpenGL doesn't have any support for it. Although I already understood the basic principles behind making a button, I did some basic research to ensure that I could implement it in OpenGL. To get these buttons to work I checked the mouse location against the button's minimum and maximum coordinates. This allowed me to tell if the user had clicked on the button or not.


1. https://weblog.jamisbuck.org/2011/2/7/maze-generation-algorithm-recap 

2. https://developer.mozilla.org/en-US/docs/Games/Techniques/3D_collision_detection

3. https://moddb.fandom.com/wiki/SDL_ttf:Tutorials:Fonts_in_OpenGL

Development 


First Stage - Maze prototyping, Maze programming

![Alt text](MayaCode.png?raw=true "MayaCode")

The first step of my development was to get the maze generator working. I used the algorithm mentioned in my initial research (1) but instead of jumping straight into OpenGL, I decided to Prototype my code inside Maya python. This was a helpful step because it allowed me to understand the algorithm quicker and get visual results a lot quicker. 

Once I could generate mazes inside of Maya I moved on to translating the code from python to an object-orientated c++ program. I decided to do this as a standalone program from any OpenGL code. This allowed me to focus more on the code rather than implementing it. The recursive backtracker works by looping through an array of cells checking to see if the neighbours of the cell have been checked. If the cell has not been checked then it creates a pathway between the two cells by removing the bordering walls. This recursively loops until all of a cell's neighbours have been checked. At this point, the algorithm backtracks until it reaches a previous cell which has got a neighbouring cell that's not been checked. My implementation of this code consisted of a Board class and a Cell class. The Board class contains all the information for the entire maze. Whereas the cell class defines the information for each square in the maze. Finally, once the maze was generated, I needed to operate it inside OpenGL. To do this I looped over the maze checking the borders of each cell to see which remained and depending on the border I rotated the walls differently and added the offset of the current cell position in the array. Then I drew each wall with its given transformation.

1. https://weblog.jamisbuck.org/2011/2/7/maze-generation-algorithm-recap 


Second stage - Ball Movement

![Alt text](mayaTurn.png?raw=true "mayaTurn")

The second stage of my development was adding a ball to my maze. Through development, I realised by rotating the maze the direction of the ball would vary in multiple axes. Therefore to simplify this stage of the development I decided to rotate the camera around the centre of the maze instead of the maze. The bonus of this was that the maze stayed flat even though it looked like it was rotating. This allowed my 3D problem to become a 2D problem. This massively simplified a lot of steps further down the line. To add movement to the ball I added an offset relative to the rotation of the camera. For example, if the camera is rotated 45 degrees on the x-axis I would move the ball 0.45f on the x-axis.  This created the illusion of the ball rolling along the maze even though it wasn't rotating.


Third stage - Box Collisions, Sphere Collision

![Alt text](Col.png?raw=true "Col")

The third stage of my development was adding collisions. At this point, I only had a maze and a rolling ball. However, there was no interaction between the two. Therefore I started developing collision detection algorithms. When checking for collisions I split the collision detection into x-axis movement and y-axis movement. This way if the ball was colliding in the x-axis but not in the y-axis I only needed to stop the movement in the x-axis allowing the ball to move along the walls. The collision algorithms themselves were inspired by (1). For this algorithm to work I needed the minimum and maximum values of the cube. Therefore I decided that the walls of the maze would be bounding boxes defined by the ngl::Bbox class. However, this class wouldn't return minimum and maximum values correctly. So I used it to store the centre, width, height and depth and calculated those values separately inside my functions. The way the collision detection algorithm works is that it clamps the ball location between the minimum and maximum values of the bounding box. This returns the closest point between the bounding box and the sphere. From there the algorithm then calculates the distance between the two points. If this distance is less than the radius of the ball a collision has occurred. This function then returns a boolean to tell if the ball can move or not depending on if it collided or not. As well as wall collisions I also needed an algorithm that detected collisions with spheres. The importance of this was that the end goal was also a sphere and the enemies that would be added later were also spheres. This algorithm was very simple and just consisted of point-point distance calculations between the two-sphere centres. If that distance was less than the two spheres' radiuses added then the balls collided. Depending on which ball collided a different set of executions would be called i.e. if the player collided with the exit the next level would be generated.   

1. https://developer.mozilla.org/en-US/docs/Games/Techniques/3D_collision_detection


Fourth stage - Buttons, Screen Rendering, Text

Once I had the base of my game finished I decided to develop some visual elements for it. I decided to add a user interface including buttons and dynamic text. This came with plenty of issues. I initially started developing and learning how to render directly to the screen. To do this I disregarded the view and project matrices so that my model was only multiplied by the model transformation. I then created a vertex and fragment shader that would be used for the screen rendering. The vertex shader took in the position of the object and the UVs allowed me to apply textures. Inside the fragment shader, I then applied the texture. Although this was very simple it required a lot of research and understanding of how the shading process works in OpenGL. Secondly, I started researching how to use text in OpenGL. I eventually settled on using an algorithm from (1). This algorithm creates text using SDL_ttf. Then it takes the text and applies it to a surface. This surface is then converted to an OpenGL texture so it can be applied to an object which will display it. This allowed me to create dynamic text as I could change the text written by SDL_ttf. Initially, this code didn't work but after some adjustments and research, I eventually managed to apply it. Finally to implement a button functionality I used the mouse position against the minimum and maximum coordinates of the buttons. For each button, I created a class called bounding which stored the centre, width and height. The class also calculated the minimum and maximum values from this data. I also added a drawing functionality. This draw function attached the button to the screen using my screen shader after transforming the object based on the width and height for scale and centre for positions. When checking the minimum and maximum against the mouse position I offset my limits so it would be in screen space in order that the min-max and mouse location would match.  

1. https://moddb.fandom.com/wiki/SDL_ttf:Tutorials:Fonts_in_OpenGL


Game mode 

![Alt text](Modes.png?raw=true "Modes") 

The final part of the development was to add some variation to my game. To do this I created three separate game modes that the player can choose between using the buttons developed in the part above. The three-game modes I created were Explore, Sandbox and two-player. In the explore game mode the player plays multiple levels increasing in difficulty each time adding one to their score until they get hit by an enemy ball. In the sandbox game mode, the player is able to alter the size of the maze which is capped for performance reasons but could be infinite. The player is also able to add and remove enemies to the game as they want. Finally, the two-player mode allows the player to play with a friend. This mode allows each player to rotate the maze twice until the turns swap. The aim is to get your ball to the centre first.


Conclusion 

In conclusion, I managed to achieve everything that I set out to do. I also learned a lot about OpenGL and how it works. Hopefully, if I'm able to set up OpenGL personally I would love to develop my game further and polish up a few of the rough edges. Furthermore, I would rethink my approach to scenes/game modes. This is because at the moment it takes a lot of work to add new ones and is something I feel I could optimise a lot by creating a class to hold each game mode. 


Refrences 

weblog.jamisbuck.org. (n.d.). Buckblog: Maze Generation: Algorithm Recap. [online] Available at: https://weblog.jamisbuck.org/2011/2/7/maze-generation-algorithm-recap [Accessed 1 March. 2022].

MDN Web Docs. (n.d.). 3D collision detection. [online] Available at: https://developer.mozilla.org/en-US/docs/Games/Techniques/3D_collision_detection. [Accessed 10 May 2022]

ModDB Wiki. (n.d.). SDL ttf:Tutorials:Fonts in OpenGL. [online] Available at: https://moddb.fandom.com/wiki/SDL_ttf:Tutorials:Fonts_in_OpenGL. [Accessed 5 Jun 2022]