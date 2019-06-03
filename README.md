minesweeper
===========

# using this repository

- fork this repository
- hack hack hack!
- (if there are improvements / clarifications to the spec please contribute!)

# spec

## main menu

The game will start with a main menu and prompts, here's the text for the menu:

```
- (s)tart game
- (l)oad game
- seed (r)andom
- (q)uit (or ^C or ^D)
*** what would you like to do?
```

print this and handle the prompts, anything which doesn't match will produce
an error.  the values in parens are shorthand so accept both `start game` and
`s` for starting a game.

- `start game` will start a game, it will prompt for the dimensions and number
  of bombs and continue from there
- `load game` will load a saved game from a file, this can make it easier to
  playtest
- `seed random` will seed your game's random number generator, this also makes
  it easier to play test (you can set a specific seed and play that game).
- `quit` is hopefully self explanatory

## start game

prompt for the width, height, and bombs

```
****** enter a width:
****** enter a height:
****** enter the number of bombs:
```

potential error messages (on errors, re-prompt):

```
!!! must be a positive integer
!!! the board dimension must not be larger than 10
!!! number of bombs exceeds the size of the board
```

after this you will enter "playing" mode which will be described below

## load game

prompt for the filename and then enter playing mode

```
****** enter the save filename:
```

potential error messages (on errors, re-prompt):

```
!!! the save file does not exist
!!! the save file is corrupted
```

## seed random

prompt for the random seed and seed your game's random object

```
****** enter the random seed:
```

potential error messages (on errors, re-prompt):

```
!!! must be a positive integer
```

## playing mode

_note_: it may make sense to implement some secret commands that help you
debug -- essentially cheat-mode.

your program will essentially follow this loop:

1. print the current board
2. prompt for a command (until successful)
3. apply the output of the command

this will continue until one of three exit conditions happens:
- save (this will send it back to the main menu)
- win (the game is completed)
- lose (the game is lost)

### printing the board

the board will be printed as follows, you can use emojis instead of characters
if you want.  Bonus points for coloring the squares so they're easier to read.

```
    0 1 2 3 4 5 6 7 8 9
  +---------------------+
0 | _  _  _  _  1  #  # |
1 | _  _  _  _  2  3  2 |
2 | _  _  _  _  *  F  F |
3 | _  _  _  _  _  _  _ |
4 | _  _  _  _  _  _  _ |
5 | _  _  _  _  _  _  _ |
6 | _  _  _  _  _  _  _ |
7 | _  _  _  _  _  _  _ |
8 | _  _  _  _  _  _  _ |
9 | _  _  _  _  _  _  _ |
  +---------------------+
```

Here I've chosen to represent the squares as the following:

- `_`: a blank square, nothing has been placed here yet
- `F`: a "flag" -- the player has marked these as "known bombs".
- `1`: (or any other number) an uncovered number square.  the number indicates
  the number of adjacent bombs.
- `#`: an uncovered empty square (essentially a zero)
- `*`: an uncovered bomb, this is only displayed while displaying the final
  losing board.

### saving the board

you get to choose the format, make sure you save enough information so that
you can restore all of the state (don't forget about your RNG)

### starting a new game

we're going to implement the tricky rules that microsoft's minesweeper does
for game starting.

in microsoft's minesweeper, the first square you click on is always a `#` from
above -- that is it doesn't have any mines around it.

the game is not generated until the first move is made.  you will use your
random number generator to pick integer positions on your boards for each of
the mines

### prompting for a move

Since screen real-estate is limited, we'll be using a minimal prompt that
allows the player to expand if needed:

```
*** [u x y,m x y,s,q,?]
```

full prompt

```
- <u x y>: uncover a bomb at x, y
- <m x y>: toggle the mark the position at x, y as a known bomb
- <s>: save the current game
- <q> (or ^C or ^D): quit the game
- <?>: help (this message)
```

On any errors, show the help and re-prompt.

```
!!! not enough arguments for command
!!! the position is already uncovered
!!! the position is off the board
!!! unknown command
```

### general gameplay

- clicking on a 0-mine (`#`) will uncover all squares around it, this also
  can trigger a chain reaction of more `#`s uncovering

### finishing the game

on game completion, draw the entire board and whether they won or lost
