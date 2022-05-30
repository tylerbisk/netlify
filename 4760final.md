---
layout: page
title: Sharks and Minnows
subtitle: Multiplayer Video Game on a PIC32
permalink: /ece4760/final
share-img: "https://i.imgur.com/aNniMuV.png"
---

# Overview
We created a multiplayer video game that runs on a PIC32 microcontroller (Photo 1).  Our game simulates predator-prey interactions in nature.  Through distinct game modes, players can experience what it is like to be both the hunter and the hunted.  The player uses a joystick to control a Salmon or a Tuna, earning points by eating minnows. The game is lost when time runs out or the player is eaten by the Shark.  

<div align="center">
    <p></p>
    <h4>Photo 1: The complete gaming system, including two joysticks, a select button, and a screen.  The wiring and PIC32 sit in the background behind the 2.4” TFT Screen</h4>
    <p></p>
    <img src="{{ "/assets/img/4760/final/Photo1_CompleteGame.jpeg" | relative_url }}" style="width: 88%"/>
</div>
<div>
    <br>
</div>

We based our game on natural predator-prey interactions between fish and their food.  During gameplay, the player uses a joystick to control their avatar: a salmon or a tuna.  As the player swims around the ocean, minnows automatically evade the player according to the boids algorithm, an algorithm that simulates flocking behavior. 

The idea for the game sprouted from an assignment in Cornell University’s ECE 4760, which required us to implement boids on a PIC32.  After successfully animating 200 boids, we added a predator for the boids to avoid.  The stationary predator activated our imaginations: “What if we could control the predator,” we thought.  The simple idea of controlling the predator developed quickly into a complete gaming system: we combined different menus, instructions, modes, players, and sound effects with nostalgic joysticks and a button to bring the experience to life.

# Detailed Game Description

<div align="center">
    <p></p>
    <h4>Figure 1: An Illustration of how the game elements appear on-screen.  The players control medium-sized red and orange dots and earn points by eating minnows, represented by small white dots.  The Shark, a large blue dot, tries to eat the players.</h4>
    <p></p>
    <img src="{{ "/assets/img/4760/final/Figure1_GameElements.png" | relative_url }}" style="width: 58%"/>
</div>
<div>
    <br>
</div>

A player may select from three different modes: Timed, Race, and Shark.  In Timed mode, one or two players eat as many minnows as they can in 60 seconds.  In Race mode, two players compete to be the first player to eat 50 minnows.  Shark mode adds another link in the food chain, challenging the player to act as both predator and prey.

In Shark mode, players can showcase their minnow-eating skills while being chased by a shark as the game boundaries shrink.  The player is slowly forced into a corner with the shark and minnows, thus increasing the difficulty of the game the longer the player survives.  Figure 1 describes how the elements are drawn. 

## Hardware Description
### Joysticks
The player interacts with the game primarily through joysticks.  Each Joystick is an 8-way input device that allows the player to move their avatar (Tuna for Player 1, Salmon for Player 2).  There are five pins on each joystick - one pin per cardinal direction plus one for ground.
			
There is no datasheet for the joysticks so we had to deduce their internal schematics ourselves.  We assumed there must be internal switches that are opened and closed when the joystick is moved.  Using a multimeter, we experimentally determined which of the five pins were activated given an input direction.  Then, once we had assigned four of the pins to a direction, we were left with one unassigned pin which we tied to ground.

To integrate the joysticks with the PIC32 microcontroller, a 10 kilo-ohm pull-up resistor was used to tie the four directional pins high to 3V3 on the PIC32, while the 5th pin was tied to ground (Figure 2).  When the joystick is moved, the pins corresponding to the direction of the movement go low, while the non-pressed pins remain high.

<div align="center">
    <p></p>
    <h4>Figure 2: Schematic	of the hardware interaction between the joysticks and the PIC32.  The basic circuitry for each of the two joysticks is the same.  Here, we see schematics for Joystick 1 (left) and Joystick 2 (right).</h4>
    <p></p>
    <img src="{{ "/assets/img/4760/final/Figure2_JoystickSchematic.png" | relative_url }}" style="width: 68%"/>
</div>
<div>
    <br>
</div>

### Select Button
The player makes selections using a pushbutton.  To do this, one terminal of the button was pulled up to 3.3 Volts via a 10 kilo-ohm resistor while the other terminal was tied to ground (Figure 3).  Our button is idle high and goes low when pressed.  We were able to debounce the button to account for false contacts that may occur during oscillations of the spring inside it.

<div align="center">
    <p></p>
    <h4>Figure 3: Schematic showing how the select button is wired to the PIC32.  The pin corresponding to the button on the PIC32 gets shorted to ground when pressed, while a resistor pulls the pin high when unpressed.</h4>
    <p></p>
    <img src="{{ "/assets/img/4760/final/Figure3_ButtonSchematic.png" | relative_url }}" style="width: 48%"/>
</div>
<div>
    <br>
</div>



## Audio
Our game comes equipped with two sound effects: “ow” and “chomp.”  When a player eats a minnow and scores a point, the minnow says “ow.”  When the shark inevitably eats the player, the shark says “chomp.”  Dr. Bruce Land kindly stepped in as our voice actor, and we recorded the samples using an iPhone.

To get the sound to play, we use DMA to send audio to a digital to analog converter, which then gets sent to a speaker.  Our DAC interfaces with the speakers via an audio socket.  We tied the front pin on the audio socket to ground, and we tied one of the two rear pins to one of DAC output A and DAC output B (Figure 4).  Then, we plug a speaker into the 3.5mm headphone jack.

We wrote a python script, utilizing the wav package, to convert the iPhone audio recordings to an array of integers.  Then, the DAC reads this array and converts each integer to a voltage to produce sound on the speakers.

We also had to make the audio file as small as possible to fit inside the PIC32’s extremely limited flash memory.  We implemented a simple compression strategy by averaging two data packets to reduce the number of audio samples by a factor of two.  The resulting sound effects took up 70kB out of the available 128.

<div align="center">
    <p></p>
    <h4>Figure 4: Schematic representation of the circuitry involved to produce sound.  The DAC is seen on the left and the Audio Socket is seen on the right.</h4>
    <p></p>
    <img src="{{ "/assets/img/4760/final/Figure4_AudioSchematic.png" | relative_url }}" style="width: 68%"/>
</div>
<div>
    <br>
</div>

## Software Description
### Overview

<div align="center">
    <p></p>
    <h4>Figure 5: A finite state machine representation of the full program.  The game boots up to the main menu.</h4>
    <p></p>
    <img src="{{ "/assets/img/4760/final/Figure5_ProgramFSM.png" | relative_url }}" style="width: 78%"/>
</div>
<div>
    <br>
</div>


When the player starts the game, they are presented with the main menu from which they can select the three different game modes as described above (Timed, Race, and Shark), as well as a fourth option to view the instructions.

After the player selects a game mode, the system asks them to confirm the number of players. The game immediately begins given the parameters chosen for the game mode and number of players.  The game automatically switches to the Game Over state when the player loses or time expires.  See Figure 5 for a depiction of the program flow.


### Menu
<div align="center">
    <p></p>
    <h4>Photo 2: A depiction of the main menu displaying the three game modes the player can choose from.  There is also an option to view the instructions.  A blue triangular cursor indicates the currently selected option.</h4>
    <p></p>
    <img src="{{ "/assets/img/4760/final/Photo2_MainMenu.jpeg" | relative_url }}" style="width: 68%"/>
</div>
<div>
    <br>
</div>


Upon reset, the player uses joystick number one to select between Timed, Race, and Shark modes, or to view the instructions.  As depicted in Photo 2, there are 50 minnows in the background of the menu that continue to animate behind the menu and give the game a dynamic feel.  A blue triangular cursor moves up and down to display which option is being selected.

If the player selects instructions, the player is greeted with a friendly screen that explains the rules at the most basic level, similar to what is seen in Figure 1.  If the player selects a game mode, we update the game state to the chosen mode and transition to player selection. Not all modes offer the same multiplayer options.  Shark mode only allows for single-player mode while Race mode is only multiplayer by nature, but if Timed mode is chosen the user must select whether they want to play in one- or two-player mode.  There is also an option to go back to the main menu.  Selecting two players activates the second joystick and displays the additional player’s score in the top left corner.

### Sound Effects
One of the main goals of this project is to animate as many minnows as possible, which requires devoting as much CPU time as possible to its animation. However, we also sought to add sound effects to the gameplay. To meet these demands without expending additional CPU cycles, we set up a DMA channel to the DAC. When the player either eats a minnow or the player loses, we execute a DMA transfer from the desired encoded sound array to the SPI channel linked to the DAC. Then we force the transfer and enable the channel to initiate the DMA transfer.

To output recordings to the DAC and fit them in memory on the PIC, they had to be compressed and resampled.  The trimming and resampling process significantly reduces the audio’s memory usage but does not affect the audio quality.  The compression by averaging, however, causes damage to the sound quality, but the distortion to the sound effects seems to fit well with the retro feel of our game.  The grainy audio quality, tactile joysticks, and standard definition TFT all come together to drive nostalgia into the player.

The original audio samples for the sound effects after a rough trim and conversion to wav format were 200 kilobytes total. Each of the two was about one second in duration and was converted from audio recordings from an iPhone at 44 kilohertz.  After resampling and compressing, we reduced our final sample data to 54 kilobytes.  Combining the audio with our code and other default libraries, our total flash memory usage is 83% - just under 110 kilobytes.


### Boids Algorithm
The minnows in the game swim around and appear to come alive thanks to the boids algorithm. Boids, or “bird-like objects”, simulates organic flocking behavior, so using this algorithm makes the game feel more natural than if the minnows moved randomly.

Much like birds in nature, a boid has a sense of where other boids in its flock are flying and will want to match the velocities of the other birds.  A boid has a `visual range` representing how far the boid can see.  However, if boids are closer than the `protected range`, the boids will swerve away from each other to avoid a collision.  By forcing our minnows to obey these rules, we tuned the `visual range` and `protected range`, along with enforcing minimum and maximum velocities, to make the minnows look less “bird-like” and more “fish-like.”

To achieve this animation, we loop through each minnow, comparing its location to the other minnows in the school, and adjust its velocities based on the values of their visual and protected range. Instead of minnows bouncing off the edge of the screen, as it reaches the border we adjust its velocity to turn around slowly and even allow it to go off-screen briefly, which gives the illusion of an infinitely sized ocean.

### Gameplay
Most of the gameplay loop stays consistent regardless of mode.  At the beginning of a game, we initialize 200 minnows.  Also, depending on the number of players, we initialize either a Tuna or both a Tuna and a Salmon (Photo 3). The game runs at 30 frames per second.
 
<div align="center">
    <p></p>
    <h4>Photo 3: A two-player game in Race mode.  Player one controls the Tuna while player two controls the Salmon.  Both players earn points by eating Minnows.  The minnows automatically avoid the players, making the game more challenging.</h4>
    <p></p>
    <img src="{{ "/assets/img/4760/final/Photo3_TwoPlayerMode.jpeg" | relative_url }}" style="width: 68%"/>
</div>
<div>
    <br>
</div> 

In each frame, the 200 minnows are erased and looped through to update their positions and their velocities.  After the minnows’ positions and velocities have been updated, we next loop through the players. We keep track of the players’ positions and update their position based on the joystick’s position.  Since the joysticks are digital, a player can choose to either remain in their current position by taking their hand off of the joystick, or move at maximum speed, which we decided to be two pixels per second in each of the vertical and horizontal directions.  Each of the 8 directions (4 cardinal directions per joystick) is mapped to a digital input to the PIC32 microcontroller.  For example, if the player pushes the joystick to the right, then we add 2 pixels to the player’s x position.  

Next, we check to see if the players have “eaten” any minnows.  If a player is touching a minnow, the minnow will be deleted, a sound effect will play, and the corresponding player’s score will be updated.  In shark mode, if the shark is touching the player, we play a sound effect and end the game.  Finally, we can redraw.  We have optimized this code as much as possible to allow all of these steps to happen in just 1/30th of a second.

#### Timed Mode
The main code for the game is the same no matter which mode the player chooses.  The difference between modes is the condition that ends the game.  In Timed mode, we switch from playing to the Game Over state when the play clock expires.  The timer starts at 60 and is decreased every second.  The amount of time remaining is also printed in the upper left corner of the TFT.

#### Race Mode
The premise for Race mode is similar to timed, but instead of playing for a finite time, we play to a finite score.  Two players race to be the first to eat 50 minnows.  A condition checks to see if either player’s score is greater than or equal to 50.

#### Shark Mode
Shark mode was the hardest mode to implement but was the most fulfilling to program and the most fun to play.  In this single-player mode, you control the tuna to eat the minnows like usual, but there is also a Shark that tries to eat you.  The shark automatically follows the player while the player frantically avoids the shark.  The ocean walls gradually cave in as the shark swims faster and minnows become sparse (Photo 4).  Similar to when a predator eats a minnow, if the shark overlaps with the player then the shark is said to have eaten the player.  In this case, the game is over and we transition to the Game Over state.

<div align="center">
    <p></p>
    <h4>Photo 4: A game being played in Shark mode where the player becomes the pursued.  The region where the player is allowed to move (dark blue) shrinks to increase difficulty as time goes on.  Minnows are allowed to exit this area briefly to maintain the appearance of life.</h4>
    <p></p>
    <img src="{{ "/assets/img/4760/final/Photo4_SharkMode.jpeg" | relative_url }}" style="width: 68%"/>
</div>
<div>
    <br>
</div> 

To add even more complexity to shark mode, the playable border decreases by 2 pixels per second.  The decrease in border size means that the shark will always be right on your tail, but also it means that the minnows will be concentrated in the center as well.  So, the longer you survive the shark, the harder it gets, but you are guaranteed to rack up more points since the minnows are more highly concentrated.  To implement this border, every half of a second a black rectangle is drawn circumscribed inside the previous rectangle.  Every second, the margin is decreased by 1 pixel.  The predator is unable to move outside the margin, and the minnows will automatically turn smoothly when it hits the margin, which is growing ever closer to the center.  The black rectangles being drawn over the blue ocean give the appearance that the ocean and thus the TFT is physically shrinking.

When the shark finally gets you, we play the “chomp” sound effect out of the DAC and to the speakers.


### Game Over
There are two Game Over screens; each screen displays depending on the number of players.  In single-player mode, the player competes against themselves, so we display the player’s final score.  However, in multiplayer mode, it is all about who wins.  In this instance, Game Over compares the scores of the two players and displays the winner.  In the event of a tie, it will display as such.

# Results
The game ended up being pretty fun.  Whether you are playing against a friend or battling against a shark, the game induces competition and gets the adrenaline pumping.  The instruction screen was the perfect balance of simplicity and explanation to understand the premise of the game.  A player who had never seen the game was able to get the fact that sharks should be avoided and minnows should be eaten to gain points.  The nuances of Race mode and Timed mode are not essential to the gameplay and can be discovered by the players after a few minutes.

The game ran smoothly and was not laggy.  Even towards the end of shark mode when the screen becomes super small and you are overloading the joystick with tons of inputs, the predator remains responsive.  When we first started making the game we chose 200 minnows as a difficult-but-achievable goal for animation.  We throttled our game at 30 frames per second and we never dipped below that threshold.  At the default clock speed of 40 megahertz at 30 frames per second, we have 1,333,333 clock cycles for each frame, which proved to be plenty of time, even with 200 minnows.  The joysticks themselves added an element of nostalgia to the system. They are very tactile and fun to actuate, keeping the player more involved than if, let's say, a keyboard was used instead.

The minnows on the menu flock and look beautiful in the background while the game starts up and the player makes their selections.  Even though the minnows in the actual game are not truly following a full ‘boids’ algorithm, the number of minnows combined with their avoidance of the predators gives the appearance of such.

The minnows instead follow a modified algorithm that we created, which simplifies the number of calculations required per frame by only updating one-fifth of the minnows according to the algorithm per frame, while the other four-fifths continue along their prior trajectory.  This reduction in computation allows us to add complexity like reading joysticks, redrawing the players, and producing sound effects - all while maintaining 200 minnows without going below 30 frames per second.


# Continuations
If we were to continue this project, we might add an option for the user to choose how long the timer runs in Timed mode.  Further, we could introduce a more intense flocking algorithm to single-player Timed mode.  This may make the game even more fun and introduce new strategies to maybe wait until the minnows flock together before going after them.  

Another customizability option would be to have different levels, something we discussed as a team but instead implemented different modes.  The easy mode would have a fast-moving predator and slow minnows that do not react to moving players unless the predator is nearby.  The hard mode would have minnows that moved more responsively and actively avoided the players.

If we had a larger budget, we would have used a larger TFT.  The larger TFT means we could see everything larger and then maybe even upgrade to four joysticks.  Using four joysticks would mean we would need either to use 4-to-1 multiplexers so we do not use four inputs for each joystick or perhaps use a port expander.  The larger TFT would take longer to erase and draw on because there are more pixels, which is something we would need to be wary of.

# Bill Of Materials
- Red 5-Pin Arcade Joystick 
- PIC 32
- TFT LCD 
- Desktop Speakers
- Audio Socket
- Breadboard
- Jumper Cables
- 10 kOhm Axial Resistor
- Push Button
- 2.1mm power jack
- MCP4822 DAC


# References

[1] Adams, Land, “Boids!”, N.D. Available: https://people.ece.cornell.edu/land/courses/ece4760/labs/f2021/lab2boids/Boids-predator.htm,  accessed October 17, 2021.  

[2] S. T. Mahbub, “tft_master.c”, N.D. Available: https://people.ece.cornell.edu/land/courses/ece4760/PIC32/TFT_display/tft_master.c.  

[3] S. T. Mahbub, “tft_master.h”, N.D. Available: https://people.ece.cornell.edu/land/courses/ece4760/PIC32/TFT_display/tft_master.h.  

[4] B. Land, “pt_cornell_1_3_2.h”, 2018. Available: https://people.ece.cornell.edu/land/courses/ece4760/PIC32/Target_board/version_1_3_2/pt_cornell_1_3_2.h.  

[5] B. Land, “pt_cornell_1_3_2.c”, 2018. Available: https://people.ece.cornell.edu/land/courses/ece4760/PIC32/Target_board/version_1_3_2/pt_cornell_1_3_2.c.  

[6] V. H. Adams, “dma_demo.c”, 2021.

