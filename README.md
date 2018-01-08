# cc3k
Rogue-like game in C++
The goal of the game is to reach the bottom of the dungeon.

# OVERVIEW

Kevin (kkqzhou)- Floor Implementation, Object Generation, Player Interaction

Milena (msbukal)- Abstract superclasses: Subject, Character, Player, Enemy and Potion along with all concrete subclasses and class Gold

Pascal (p35nguye)- Plan of Attack, UML Diagram, Keybinding DLC

# UML DIAGRAM
![uml-diagram](https://github.com/msbukal/cc3k/blob/master/cc3k_UML.png?raw=true)

# HOW TO PLAY

Compile using "make". Execute base game (without DLC) using "./cc3k".

### SAMPLE IMAGES
![start](https://github.com/msbukal/cc3k/blob/master/sample_images/1-start.JPG?raw=true)
![spawn](https://github.com/msbukal/cc3k/blob/master/sample_images/2-spawn.JPG?raw=true)
![attack](https://github.com/msbukal/cc3k/blob/master/sample_images/3-attack.JPG?raw=true)
![death](https://github.com/msbukal/cc3k/blob/master/sample_images/4-death.JPG?raw=true)

### COMMANDS

Game demands player race or quits (q or EOF). A valid race shortform will start game with that race.

Player can input commands, and at each command the board is redrawn:
- no, so, ea, we, ne, nw, se, sw: to move player one block in cardinal direction
- u <direction>: uses the potion in direction
- a <direction>: attacks in direction if an enemy is there
- s, d, v, g, t: specifies the race the player wishes to be when starting a game
- f: stops enemies from moving until reentered
- r: restarts the game, all states/inventory/gold/race are reset
- q: quit and exit the game

Play will continue as specified until:
- player restarts
- player reaches end of floor 5 (end of dungeon)
- player dies
- player quits

### PLAYER
Player represented by ‘@’.

Player Races:

| Race | Shortform | Starting/Max HP | Atk | Def | Special Effects |
| ---- | --------- | --------------- | --- | --- | --------------- |
| Shade | s | 125 | 25 | 25 | 50% increase to score |
| Drow | d | 150 | 25 | 15 | Potion effects magnified by 1.5 |
| Vampire | v | 50, no max | 25 | 25 | Gains 5 HP for every successful attack |
| Troll | t | 120 | 25 | 15 | Regains 5 HP every turn | 
| Goblin | g | 110 | 15 | 20 | Steals 5 gold from every slain enemy |

### ENEMY
Enemy Races:

| Enemy | Marker | HP | Atk | Def | Special Effects |
| ----- | ------ | -- | --- | --- | --------------- |
| Human | H | 140 | 20 | 20 | Drops 2 normal piles of gold |
| Dwarf | W | 100 | 20 | 30 | Vampires lose 5HP instead of gaining |
| Elf | E | 140 | 30 | 10 | 2 attacks against all but drow |
| Orc | O | 180 | 30 | 25 | 50% more damage to goblins |
| Merchant | M | 30 | 70 | 5 | Neutral by default but can attack and anger all merchants in room |
| Dragon | D | 150 | 20 | 20 | Guards a treasure hoards |
| Halfling | L | 100 | 15 | 20 | 50% that player misses in combat |

Other than Humans, Dragons and Merchants, enemies will drop either a small or normal pile of gold.

Other than Dragons, enemies move randomly 1 tile at a time (to empty tiles). Enemies can not leave their birth room. Movement is done starting at leftmost enemy in a row.

If a human is within a 1 block radius of an enemy, it will attack. (Merchants only if angered).

### ITEMS

##### Potions

| Potion | Marker | Polarity | Effect | 
| ------ | ------ | -------- | ------ |
| Restore Health | RH | Positive | Restore up to 10HP, can not exceed max |
| Boost Atk | BA | Positive | Increase ATK by 5 |
| Boost Def | BD | Positive | Increase DEF by 5 |
| Poison Health | PH | Negative | Lose up to 10HP, can not fall below 0 |
| Wound Atk | WA | Negative | Decrease ATK by 5 | 
| Wound Def | WD | Negative | Decrease ATK by 5 |

#### Treasure
Consists of gold in certain piles: 
- Null (value 0)
- Small (value 1)
- Normal (value 2)
- Merchant (value 4)
- Dragon hoard (value 6)

Dragon hoard protected by a dragon and can only be picked up once dragon is slain.

Gold is denoted on map with a G. 

Gold dropped by merchant (4) or human (2x2) are only picked up after player walks over.

### FLOORS
Five chambers. Player spawns randomly but never in the chamber with the stairs going down to the next level.

Each floor has:
- 1 stairway ‘\’ going down. Room is random.
- 10 potions with each type chosen at random, random floor tiles in rooms.
- 10 piles of gold per floor at random floor tiles in rooms, varying spawn rates.
- 20 enemies per floor not including dragons, chances of enemies are at random floor tiles in rooms vary.
        
When player moves onto a stairway, the next level is generated.

### COMBAT
All enemies except Merchant and Dragon are hostile. Hostile enemies will attempt to attack the player when they are within a 1 block radius, before player can attack. 

Dragon will attack only if player is in a 1 block radius of the dragon hoard. Merchants attack back if attacked and other merchants will become hostile.

If player is not within radius, enemies resume random movement.

# KEYBIND DLC
We decided that typing in commands for movement and actions was far too inconvenient for users. As such, we decided to implement keyboard controls using getch, removing the need to press “enter” every time the user wants to perform an action in-game. Looking into various methods for keybinding, we initially opted to use the ncurses library; however, this proved to be incompatible with our method of rendering the map using cout. As such, we used a custom getch function using getchar and termios to turn echo off. 

To compile with dlc, execute "./cc3k -dlc" after make

The commands that change are:

| Command | Key |
| ------- | --- |
| Start Game/Restart | Enter |
| Exit Game | Esc |
| Freeze Monsters (for testing) | ` |
| Move (no, ne, ea, se, so, sw, we, nw) | W, E, D, C, X, Z, A, Q |
| Attack | O + [direction] |
| Use Potion | P + [direction] |
| Choose player race (s, d, v, g, t, h = Sheep) | 1, 2, 3, 4, 5, 6 |
