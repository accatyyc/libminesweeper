[![Build Status](https://travis-ci.org/accatyyc/libminesweeper.svg?branch=master)](https://travis-ci.org/accatyyc/libminesweeper)
# libminesweeper 
A static library for handling Minesweeper game logic, that can run on embedded hardware. 
Its goals are to be as efficient as possible memory- and processing power wise. It's written
in "[SDCC](http://sdcc.sourceforge.net) compatible C99", which pretty much means that it's
C99 without inline variable declarations.

## Building
Clone the repo and run `make` in the folder, and you'll get the binary `libminesweeper.a`. Link the binary and include 
`include/minesweeper.h` in your projects. You can also check out the reference implentations below. Those repos include this repo
as a `git submodule` and automatically builds it as part of their build chain.

## Usage

First, we should include the library headers:
```c
#include <minesweeper.h>
```

Let's set up a game board. You can have as many boards as you want if you for example want to implement multiplayer.
First, you need to supply the library with a memory location where it can put board data. The minimum required length
of this buffer can be retrieved from the function `minimum_buffer_size(int width, int height)`.

To initialize a board in this buffer, call `init_board(int width, int height, float mine_density, uint8_t *buffer)`,
where mine density is a float value between 0 and 1. If 1, all tiles will contain a mine, and if 0, no tiles will
contain a mine. The game starts to get unplayable around a density of 0.5.
	
```c
int width = 20;
int height = 10;
uint8_t *board_buffer = malloc(minimum_buffer_size(width, height));

srand(time(NULL)); // Let's make the game less predictable
struct board *board = board_init(width, height, 0.1, board_buffer);
```

Next, let's move the cursor around and open a tile. Check out `minesweeper.h` for more stuff you can do.
```c
move_cursor(board, RIGHT);
open_tile_at_cursor(board);
```

To render tiles correctly in your UI, you can check out the reference implementations. But the quick
answer is that each "tile" is an 8-bit number that contains all info about that tile. To check if a
tile contains a flag or is opened, you can do:
```c
uint8_t *tile = get_tile_at(board, board->cursor_x, board->cursor_y);
bool contains_flag = *tile & TILE_FLAG;
bool is_opened = *tile & TILE_OPENED;
```

## Testing
Run `make test` to run the unit tests. It might be a good idea to run the tests
with your preferred compiler, to catch anything I might've missed. Please add an
issue if any tests don't pass!

## Reference implementations:
- [Terminal Mines](https://github.com/accatyyc/terminal-mines) An ncurses frontend for running in terminals
- [gbmines](https://github.com/rotmoset/gb-mines) A Gameboy Color frontend