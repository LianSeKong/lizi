# Snake

> creating a playable snake game

## Conceptual Overview

### Snakes

A snake game can be represented by a grid of characters. 

The grid contains walls, fruits, and one or more snakes. 

An example of a game is shown below:

```javascript
##############
#            #
#    dv      #
#     v   #  #
#     v   #  #
#   s >>D #  #
#   v     #  #
# *A<  *  #  #
#            #
##############
```

The grid has the following special characters:

*   `#` denotes a wall.
*   (space character) denotes an empty space.
*   `*` denotes a fruit.
*   `wasd` denotes the tail of a snake.
*   `^<v>` denotes the body of a snake.
*   `WASD` denotes the head of a snake.
*   `x` denotes the head of a snake that has died.

Each character of the snake tells you what direction the snake is currently heading in:

*   `w`, `W`, or `^` denotes up
*   `a`, `A`, or `<` denotes left
*   `s`, `S`, or `v` denotes down
*   `d`, `D`, or `>` denotes right

At each time step, each snakes moves according to the following rules:

*   Each snake moves one step in the direction of its head.
*   If the head crashes into the body of a snake or a wall, the snake dies and stops moving. When a snake dies, the head is replaced with an `x`.
*   If the head moves into a fruit, the snake eats the fruit and grows by 1 unit in length. Each time fruit is consumed, a new fruit is generated on the board.

In the example above, after one time step, the board will look like this:

```
##############
#         *  #
#     s      #
#     v   #  #
#     v   #  #
#   s >>>D#  #
#   v     #  #
# A<<  *  #  #
#            #
##############
```

After one more time step, the board will look like this:

```
##############
#         *  #
#     s      #
#     v   #  #
#     v   #  #
#     >>>x#  #
#   s     #  #
#A<<<  *  #  #
#            #
##############
```

Snakes are guaranteed to be at least three units long.

### Numbering snakes

Each snake on the board is numbered depending on the position of its tail, in the order that the tails appear in the file (going from top-to-bottom, then left-to-right). For example, consider the following board with four snakes:

```
#############
#  s  d>>D  #
#  v   A<a  #
#  S    W   #
#       ^   #
#       w   #
#############
```

Snake 0 is the snake with tail `s`, snake 1 has tail `d`, snake 2 has tail `a`, and snake 3 has tail `w`.

Once the snakes are numbered from their initial positions, the numbering of the snakes does not change throughout the game.

### Game board

A game board is a grid of characters, not necessarily rectangular. Here's an example of a non-rectangular board:

```
##############
#            #######
#####             ##
#   #             ##
#####             ######
#                 ##   #
#                 ######
#                 ##
#                  #
#      #####       #
########   #########
```

Note that each row can have a different number of characters, but will start and end with a wall (`#`). You can also assume that the board is an enclosed space, so snakes can't travel infinitely far in any direction.

### The `game_state_t` struct

A snake game is stored in memory in a `game_state_t` struct, which is defined in `state.h`. The struct contains the following fields:

*   `unsigned int num_rows`: The number of rows in the game board.
*   `char** board`: The game board in memory. Each element of the `board` array is a `char*` pointer to a character array containing a row of the board. Each row must be terminated by a new line character and must be a valid string.
*   `unsigned int num_snakes`: The number of snakes on the board.
*   `snake_t* snakes`: An array of `snake_t` structs.

### The `snake_t` struct

Also defined in `state.h`, each `snake_t` struct contains the following fields:

*   `unsigned int tail_row`: The row of the snake's tail.
*   `unsigned int tail_col`: The column of the snake's tail.
*   `unsigned int head_row`: The row of the snake's head.
*   `unsigned int head_col`: The column of the snake's head.
*   `bool live`: `true` if the snake is alive, and `false` if the snake is dead.

Please don't modify the provided struct definitions. You should only need to modify `state.c` `snake.c`, and `custom_tests.c` in this project.

## Task 1: `create_default_state`

Implement the `create_default_state` function in `state.c`. This function should create a default snake game in memory with the following starting state (which you can hardcode), and return a pointer to the newly created `game_state_t` struct.

```
####################
#                  #
# d>D    *         #
#                  #
#                  #
#                  #
#                  #
#                  #
#                  #
#                  #
#                  #
#                  #
#                  #
#                  #
#                  #
#                  #
#                  #
####################
```

<table><colgroup><col span="1"> <col span="1"> <col span="1"> </colgroup><tbody><tr><td colspan="3"><code>create_default_state</code></td></tr><tr><td><b>Arguments</b></td><td colspan="2">None</td></tr><tr><td><b>Return values</b></td><td><code>game_state_t *</code></td><td>A pointer to the newly created <code>game_state_t</code> struct.</td></tr></tbody></table>

### Hints

*   The board has 18 rows, and each row has 20 columns. The fruit is at row 2, column 9 (zero-indexed). The tail is at row 2, column 2, and the head is at row 2, column 4.
*   Which part of memory (code, static, stack, heap) should you store the new game in?
*   `strcpy` may be helpful.



## Solution

```c
/* Task 1 */
game_state_t *create_default_state() {
  unsigned int num_cols = 20;
  game_state_t *state = (game_state_t *)malloc(sizeof(game_state_t));
  state->num_rows = 18;
  state->num_snakes = 1;

  // init board
  state->board = (char **)calloc(state->num_rows, sizeof(char *));
  for (size_t i = 0; i < state->num_rows; i++) {
    state->board[i] = (char *)calloc(num_cols + 1, sizeof(char));
  }
  // begin an end row
  strcpy(state->board[0], "####################");
  strcpy(state->board[state->num_rows - 1], "####################");
  // main body
  for (unsigned int i = 1; i < state->num_rows - 1; i++) {
    strcpy(state->board[i], "#                  #");
  }

  // init snake
  state->snakes = (snake_t *)malloc(sizeof(snake_t) * state->num_snakes);
  state->snakes[0].tail_row = 2;
  state->snakes[0].tail_col = 2;
  state->snakes[0].head_row = 2;
  state->snakes[0].head_col = 4;
  state->snakes[0].live = true;

  // init fruit  
  state->board[2][9] = '*';

  // render board
  snake_t snake = state->snakes[0];
  state->board[2][2] = 'd';
  state->board[snake.tail_row][snake.tail_col] = 'd';
  state->board[snake.head_row][snake.head_col] = 'D';
  state->board[snake.head_row][snake.head_col - 1] = '>';
  return state;
}

```





## Task 2: `free_state`

Implement the `free_state` function in `state.c`. This function should free all memory allocated for the given state, including all `snake` structs and all `state`->`board` contents.

<table><colgroup><col span="1"> <col span="1"> <col span="1"> </colgroup><tbody><tr><td colspan="3"><code>free_state</code></td></tr><tr><td><b>Arguments</b></td><td><code>game_state_t* state</code></td><td>A pointer to the <code>game_state_t</code> struct to be freed</td></tr><tr><td><b>Return values</b></td><td colspan="2">None</td></tr></tbody></table>

## Solution

```c
/* Task 2 */
void free_state(game_state_t *state)
{
  for (size_t i = 0; i < state->num_rows; i++) {
    free(state->board[i]);
  }
  free(state->snakes);
  free(state->board);
  free(state);
  return;
}
```







## Task 3: `print_board`

Implement the `print_board` function in `state.c`. This function should print out the given game board to the given file pointer.

<table><colgroup><col span="1"> <col span="1"> <col span="1"> </colgroup><tbody><tr><td colspan="3"><code>print_board</code></td></tr><tr><td rowspan="2"><b>Arguments</b></td><td><code>game_state_t* state</code></td><td>A pointer to the <code>game_state_t</code> struct to be printed</td></tr><tr><td><code>FILE* fp</code></td><td>A pointer to the file object where the board should be printed to</td></tr><tr><td><b>Return values</b></td><td colspan="2">None</td></tr></tbody></table>

### Hints

*   The `fprintf` function will help you print out characters and/or strings to a given file pointer.

## Solution

```c
void print_board(game_state_t *state, FILE *fp)
{
  for (size_t i = 0; i < state->num_rows; i++) {
    fprintf(fp, "%s\n", state->board[i]);
  }
  return;
}
```



## Task 4: `update_state`

Implement the `update_state` function in `state.c`. This function should move the snakes one timestep according to the rules of the game.

Helper functions are not graded; for this task, we'll only be checking that `update_state` is correct.

### Task 4.1: Helpers

We have provided the following helper function definitions that you can implement. These functions are entirely independent of any game board or snake; they only take in a single character and output some information about that character.

*   `bool is_tail(char c)`: Returns true if `c` is part of the snake's tail. The snake's tail consists of these characters: `wasd`. Returns false otherwise.
*   `bool is_head(char c)`: Returns true if `c` is part of the snake's head. The snake's head consists of these characters: `WASDx`. Returns false otherwise.
*   `bool is_snake(char c)`: Returns true if `c` is part of the snake. The snake consists of these characters: `wasd^<v>WASDx`. Returns false otherwise.
*   `char body_to_tail(char c)`: Converts a character in the snake's body (`^<v>`) to the matching character representing the snake's tail (`wasd`). The output may be undefined for characters that are not a snake's body.
*   `char head_to_body(char c)`: Converts a character in the snake's head (`WASD`) to the matching character representing the snake's body (`^<v>`). The output may be undefined for characters that are not a snake's head.
*   `unsigned int get_next_row(unsigned int cur_row, char c)`: Returns `cur_row + 1` if `c` is `v` or `s` or `S`. Returns `cur_row - 1` if `c` is `^` or `w` or `W`. Returns `cur_row` otherwise.
*   `unsigned int get_next_col(unsigned int cur_col, char c)`: Returns `cur_col + 1` if `c` is `>` or `d` or `D`. Returns `cur_col - 1` if `c` is `<` or `a` or `A`. Returns `cur_col` otherwise.

Unit tests are not provided for these helper functions, so you'll have to write your own tests in `custom_tests.c` to make sure that these are working as expected. Make sure that these tests comprehensively test your helper functions--our autograder will run your tests on buggy implementations to make sure that your tests can catch bugs!

When writing a unit test, the test function should return `false` if the test fails, and `true` if the test passes. You can use `printf` to print out debugging statements. Some of the assert helper functions in `asserts.h` might be useful.

Once you've written your own unit tests, you can run them with `make run-custom-tests` and `make debug-custom-tests`.

## Solution

```c
static bool is_tail(char c) {
  return c == 'w' || c == 'a' || c == 's' || c == 'd';
}
static bool is_head(char c) {
  return c == 'W' || c == 'A' || c == 'S' || c == 'D' || c == 'x';
}
static bool is_snake(char c) {
  return is_tail(c) || is_head(c) || c == 'v' || c == '^' || c == '<' || c == '>';
}
static char body_to_tail(char ch) {
  switch (ch) {
      case '^':
          ch = 'w';
          break;
      case 'v':
          ch = 's';
          break;
      case '>':
          ch = 'd';
          break;
      case '<':
          ch = 'a';
          break;
      default:
          ch = '?';
          break;
  }
  return ch;
}
static char head_to_body(char c) {
  char cb;
  switch (c) {
      case 'W':
        cb = '^';
        break;
      case 'S':
        cb = 'v';
        break;
      case 'A':
        cb = '<';
        break;
      case 'D':
        cb = '>';
        break;
      default:
        cb = '?';
  }
  return cb;
}

static unsigned int get_next_row(unsigned int cur_row, char c) {
  if (c == 'v' || c == 's' || c == 'S') {
    cur_row += 1;
  }
  else if (c == '^' || c == 'w' || c == 'W') {
    cur_row -= 1;
  }
  return cur_row;
}

static unsigned int get_next_col(unsigned int cur_col, char c) {
  if (c == '>' || c == 'd' || c == 'D') {
    cur_col += 1;
  }
  else if (c == '<' || c == 'a' || c == 'A') {
    cur_col -= 1;
  }
  return cur_col;
}
```



### Task 4.2: `next_square`

Implement the `next_square` helper function in `state.c`. This function returns the character in the cell the given snake is moving into. This function should not modify anything in the game stored in memory.

<table><colgroup><col span="1"> <col span="1"> <col span="1"> </colgroup><tbody><tr><td colspan="3"><code>next_square</code></td></tr><tr><td rowspan="2"><b>Arguments</b></td><td><code>game_state_t* state</code></td><td>A pointer to the <code>game_state_t</code> struct to be analyzed</td></tr><tr><td><code>int snum</code></td><td>The index of the snake to be analyzed</td></tr><tr><td><b>Return values</b></td><td><code>char</code></td><td>The character in the cell the given snake is moving into</td></tr></tbody></table>

As an example, consider the following board:

```
##############
#            #
#            #
#            #
#   d>D*     #
#            #
#       s    #
#       v    #
#       S    #
##############
```

Assuming that `state` is a pointer to this game state, then `next_square(state, 0)` should return `*`, because the head of snake 0 is moving into a cell with `*` in it. Similarly, `next_square(state, 1)` should return `#` for snake 1.

The helper functions you wrote earlier might be helpful for this function (and the rest of this task too). Also, check out `get_board_at` and `set_board_at`, which are helper functions we wrote for you.

Use `make run-unit-tests` and `make debug-unit-tests` to run the provided unit tests. You can also use `p print_board(state, stdout)` to print out your entire board while debugging in `cgdb`.



## Solution

```c
static char next_square(game_state_t *state, unsigned int snum) {
  snake_t snake = state->snakes[snum];
  char head_char = state->board[snake.head_row][snake.head_col];
  unsigned int next_col = get_next_col(snake.head_col, head_char);
  unsigned int next_row = get_next_row(snake.head_row, head_char);
  return get_board_at(state, next_row, next_col);
}
```



### Task 4.3: `update_head`

Implement the `update_head` function in `state.c`. This function will update the head of the snake.

Remember that you will need to update the head both on the game board and in the `snake_t` struct. On the game board, add a character where the snake is moving. In the `snake_t` struct, update the row and column of the head.

<table><colgroup><col span="1"> <col span="1"> <col span="1"> </colgroup><tbody><tr><td colspan="3"><code>update_head</code></td></tr><tr><td rowspan="2"><b>Arguments</b></td><td><code>game_state_t* state</code></td><td>A pointer to the <code>game_state_t</code> struct to be updated</td></tr><tr><td><code>int snum</code></td><td>The index of the snake to be updated</td></tr><tr><td><b>Return values</b></td><td colspan="2">None</td></tr></tbody></table>

As an example, consider the following board:

```
##############
#   d>D      #
#        *   #
#        W   #
#        ^   #
#        ^   #
#        w   #
#            #
#            #
##############


```

Assuming that `state` is a pointer to this game state, then `update_head(state, 0)` will move the head of snake 0, leaving all other snakes unchanged. In the `snake_t` struct corresponding to snake 0, the `head_col` value should be updated from 6 to 7, and the `head_row` value should stay unchanged at 1. The new board will look like this:

```
##############
#   d>>D     #
#        *   #
#        W   #
#        ^   #
#        ^   #
#        w   #
#            #
#            #
##############


```

Note that this function ignores food, walls, and snake bodies when moving the head.

## Solution

```c
static void update_head(game_state_t *state, unsigned int snum) {
  snake_t *snake = (state->snakes) + snum;
  unsigned int cur_row = snake->head_row;
  unsigned int cur_col = snake->head_col;
  char cur_char = state->board[cur_row][cur_col];
  unsigned int next_row = get_next_row(cur_row, cur_char);
  unsigned int next_col = get_next_col(cur_col, cur_char);
  set_board_at(state, next_row, next_col, cur_char);
  set_board_at(state, cur_row, cur_col, head_to_body(cur_char));
  snake->head_row = next_row;
  snake->head_col = next_col;
  return;
}
```



### Task 4.4: `update_tail`

Implement the `update_tail` function in `state.c`. This function will update the tail of the snake.

Remember that you will need to update the tail both on the game board and in the `snake_t` struct. On the game board, blank out the current tail, and change the new tail from a body character (`^<v>`) into a tail character (`wasd`). In the `snake_t` struct, update the row and column of the tail.

<table><colgroup><col span="1"> <col span="1"> <col span="1"> </colgroup><tbody><tr><td colspan="3"><code>update_tail</code></td></tr><tr><td rowspan="2"><b>Arguments</b></td><td><code>game_state_t* state</code></td><td>A pointer to the <code>game_state_t</code> struct to be updated</td></tr><tr><td><code>int snum</code></td><td>The index of the snake to be updated</td></tr><tr><td><b>Return values</b></td><td colspan="2">None</td></tr></tbody></table>

As an example, consider the following board:

```
##############
#   d>D      #
#        *   #
#        W   #
#        ^   #
#        ^   #
#        w   #
#            #
#            #
##############
```

Assuming that `state` is a pointer to this game state, then `update_tail(state, 1)` will move the tail of snake 1, leaving all other snakes unchanged. In the `snake_t` struct corresponding to snake 1, the `tail_row` value should be updated from 6 to 5, and the `tail_col` value should stay unchanged at 9. The new board will look like this:

```
##############
#   d>D      #
#        *   #
#        W   #
#        ^   #
#        w   #
#            #
#            #
#            #
##############
```

## Solution

```c
static void update_tail(game_state_t *state, unsigned int snum) {
  snake_t *snake_ptr = &state->snakes[snum];
  char tail_char = state->board[snake_ptr->tail_row][snake_ptr->tail_col];
  unsigned int next_row = get_next_row(snake_ptr->tail_row, tail_char);
  unsigned int next_col = get_next_col(snake_ptr->tail_col, tail_char);
  set_board_at(state, snake_ptr->tail_row, snake_ptr->tail_col, ' ');
  set_board_at(state, next_row, next_col, body_to_tail(state->board[next_row][next_col]));
  snake_ptr->tail_row = next_row;
  snake_ptr->tail_col = next_col;
  return;
}
```



### Task 4.5: `update_state`

Using the helpers you created, implement `update_state` in `state.c`.

As a reminder, the rules for moving a snake are as follows:

*   Each snake moves one step in the direction of its head.
*   If the head crashes into the body of a snake or a wall, the snake dies and stops moving. When a snake dies, the head is replaced with an `x`.
*   If the head moves into a fruit, the snake eats the fruit and grows by 1 unit in length. (You can implement growing by 1 unit by updating the head without updating the tail.) Each time fruit is consumed, a new fruit is generated on the board.

The `int (*add_food)(game_state_t* state)` argument is a function pointer, which means that `add_food` is a pointer to the code section of memory. The code that `add_food` is pointing at is a function that takes in `game_state_t* state` as an argument and returns an `int`. You can call this function with `add_food(x)`, replacing `x` with your argument, to add a fruit to the board.

<table><colgroup><col span="1"> <col span="1"> <col span="1"> </colgroup><tbody><tr><td colspan="3"><code>update_state</code></td></tr><tr><td rowspan="2"><b>Arguments</b></td><td><code>game_state_t* state</code></td><td>A pointer to the <code>game_state_t</code> struct to be updated</td></tr><tr><td><code>int (*add_food)(game_state_t* state)</code></td><td>A pointer to a function that will add fruit to the board</td></tr><tr><td><b>Return values</b></td><td colspan="2">None</td></tr></tbody></table>

## Solution

```c
void update_state(game_state_t *state, int (*add_food)(game_state_t *state))
{
  for (unsigned int i = 0; i < state->num_snakes; i++)
  {
    snake_t *snake_ptr = state->snakes + i;
    char head_char = get_board_at(state, snake_ptr->head_row, snake_ptr->head_col);
    unsigned int next_row = get_next_row(snake_ptr->head_row, head_char);
    unsigned int next_col = get_next_col(snake_ptr->head_col, head_char);
    char next_char = get_board_at(state, next_row, next_col);
    if (is_snake(next_char) || next_char == '#') {
      snake_ptr->live = false;
      set_board_at(state, snake_ptr->head_row, snake_ptr->head_col, 'x');
    } else if (next_char == '*') {
      update_head(state, i);
      add_food(state);
    } else {
      update_head(state, i);
      update_tail(state, i);
    }
  }
  return;
}
```





## Task 5: `load_board`

Implement the `load_board` function in `state.c`. This function will read a game board from a stream (`FILE *`) into memory. Your implementation of `load_board` must support reading in from `stdin` and any other streams, so please do not use anything that does not support `stdin`, such as seeking, rewinding, or reopening.

Remember that each row of the game board might have a different number of columns. Your implementation should be memory-efficient and should not allocate significantly more memory than necessary to store the board. For example, if a row is 3 characters long, you shouldn't be allocating 100 bytes of space for that row.

You must use `fgets` to read from the file pointer. We reserve the ability to manually regrade your submission if it uses a function other than `fgets` to read from `file`. Other string functions, such as `strchr`, may be helpful here as well!

Hint: `realloc` may be helpful for this task.

Tasks 5 and 6 combined will create a `game_state_t` struct in memory with all its fields set up. In this task, please set `num_snakes` to 0 and set the `snakes` array to `NULL`, since these will be initialized in task 6.

### Task 5.1: `read_line`

Implement the `read_line` function in `state.c`. Given a `FILE *` `file`, read a line from `file` and store the string on the heap. If `fgets` errors, return `NULL`.

<table><colgroup><col span="1"> <col span="1"> <col span="1"> </colgroup><tbody><tr><td colspan="3"><code>read_line</code></td></tr><tr><td><b>Arguments</b></td><td><code>FILE* file</code></td><td>A file pointer where the string can be read from</td></tr><tr><td><b>Return values</b></td><td><code>char *</code></td><td>A pointer to the newly read string. <code>NULL</code> if there are any errors, or if <code>EOF</code> is reached.</td></tr></tbody></table>

## Solution

```c
char *read_line(FILE *fp) {
  char *str = (char *) malloc(sizeof(char) * 255);
  if (fgets(str, 255, fp) == NULL) {
    return NULL;
  }
  while (str[strlen(str) - 1] != '\n') {
    str = realloc(str, sizeof(char) * strlen(str) * 2);
    fgets(str + strlen(str), 255, fp);
  }
  str = realloc(str, sizeof(char) * (strlen(str) + 1));
  return str;
}
```



### Task 5.2: `load_board`

Using `read_line`, implement the `load_board` function in `state.c`.

<table><colgroup><col span="1"> <col span="1"> <col span="1"> </colgroup><tbody><tr><td colspan="3"><code>load_board</code></td></tr><tr><td><b>Arguments</b></td><td><code>FILE* file</code></td><td>A file pointer where the board can be read from</td></tr><tr><td><b>Return values</b></td><td><code>game_state_t *</code></td><td>A pointer to the newly created <code>game_state_t</code> struct. <code>NULL</code> if there are any errors.</td></tr></tbody></table>

## Solution

```c
game_state_t *load_board(FILE *fp) {
  unsigned int capacity = 255;
  game_state_t* state = (game_state_t *) malloc(sizeof(game_state_t));
  state->num_snakes = 0;
  state->snakes = NULL;
  state->num_rows = 0;
  state->board = (char **)calloc(capacity, sizeof(char *));
  char* str;
  while ((str = read_line(fp)) != NULL) {
    if (str[strlen(str) - 1] == '\n') {
      str[strlen(str) - 1] = '\0';
    }
    state->board[(state->num_rows)++] = str;
    if (state->num_rows >= capacity) {
      capacity *= 2;
      state->board = (char **) realloc(state->board, sizeof(char *) * capacity);
    }
  }
  state->board = (char **) realloc(state->board, sizeof(char *) * state->num_rows);
  return state;
}
```



## Task 6: `initialize_snake`

Implement the `initialize_snake` function in `state.c`. This function takes in a game board and creates the array of `snake_t` structs.

### Task 6.1: `find_head`

Implement the `find_head` function in `state.c`. Given a `snake_t` struct with the tail row and column filled in, this function traces through the board to find the head row and column, and fills in the head row and column in the struct.

<table><colgroup><col span="1"> <col span="1"> <col span="1"> </colgroup><tbody><tr><td colspan="3"><code>find_head</code></td></tr><tr><td rowspan="2"><b>Arguments</b></td><td><code>game_state_t* state</code></td><td>A pointer to the <code>game_state_t</code> struct to be analyzed</td></tr><tr><td><code>int snum</code></td><td>The index of the snake to be analyzed</td></tr><tr><td><b>Return values</b></td><td colspan="2">None</td></tr></tbody></table>

As an example, consider the following board:

```
##############
#            #
#        *   #
#            #
#   d>v      #
#     v      #
#  W  v      #
#  ^<<<      #
#            #
##############


```

Assuming that `state` is a pointer to this game state, then `find_head(state, 0)` will fill in the `head_row` and `head_col` fields of the snake 0 struct with 6 and 3, respectively.

## Solution

```c
static void find_head(game_state_t *state, unsigned int snum) {
  snake_t * snake_ptr = (state->snakes) + snum;
  unsigned int row = snake_ptr->tail_row;
  unsigned int col = snake_ptr->tail_col;
  char ch = state->board[row][col];
  while (is_snake(ch) && !is_head(ch)) {
    row = get_next_row(row, ch);
    col = get_next_col(col, ch);
    ch = state->board[row][col];
  }
  snake_ptr->head_row = row;
  snake_ptr->head_col = col;
  return;
}
```



### Task 6.2: `initialize_snake`

Using `find_head`, implement the `initialize_snake` function in `state.c`. You can assume that the state passed into this function is the result of calling `load_board`, but you may not assume that the `snakes` array is defined. This means the board-related fields are already filled in, and you only need to fill in `num_snakes` and create the `snakes` array.

You may assume that all snakes on the board start out alive.

<table><colgroup><col span="1"> <col span="1"> <col span="1"> </colgroup><tbody><tr><td colspan="3"><code>initialize_snakes</code></td></tr><tr><td><b>Arguments</b></td><td><code>game_state_t* state</code></td><td>A pointer to the <code>game_state_t</code> struct to be filled in</td></tr><tr><td><b>Return values</b></td><td><code>game_state_t* state</code></td><td>A pointer to the <code>game_state_t</code> struct with fields filled in. This can be the same as the struct passed in (you can modify the struct in-place).</td></tr></tbody></table>

## Solution

```c
game_state_t *initialize_snakes(game_state_t *state) {
  state->snakes = (snake_t *)malloc(sizeof(snake_t) * 512);
  state->num_snakes = 0;
  for (unsigned int i = 0; i < state->num_rows; i++)
  {
    char* row = state->board[i];

    for (unsigned int j = 0; j < strlen(row); j++)
    {
      
      char ch = row[j];
      if (is_tail(ch)) {
        snake_t* snake = state->snakes + (state->num_snakes);
        snake->live = true;
        snake->tail_row = i;
        snake->tail_col = j;
        state->num_snakes += 1;
      }
    }
  }
  for (unsigned int i = 0; i < state->num_snakes; i++) {
    find_head(state, i);
  }
  print_board(state, stdout);
  return state;
}
```



## Task 7: `main`

Using the functions you implemented in all the previous tasks, fill in the blanks in `snake.c`. Each time the `snake.c` program is run, the board will be updated by one time step.

## Solution

```c
int main(int argc, char *argv[]) {
  bool io_stdin = false;
  char *in_filename = NULL;
  char *out_filename = NULL;
  game_state_t *state = NULL;

  for (int i = 1; i < argc; i++) {
    if (strcmp(argv[i], "-i") == 0 && i < argc - 1) {
      if (io_stdin) {
        fprintf(stderr, "Usage: %s [-i filename | --stdin] [-o filename]\n", argv[0]);
        return 1;
      }
      in_filename = argv[i + 1];
      i++;
      continue;
    } else if (strcmp(argv[i], "--stdin") == 0) {
      if (in_filename != NULL) {
        fprintf(stderr, "Usage: %s [-i filename | --stdin] [-o filename]\n", argv[0]);
        return 1;
      }
      io_stdin = true;
      continue;
    }
    if (strcmp(argv[i], "-o") == 0 && i < argc - 1) {
      out_filename = argv[i + 1];
      i++;
      continue;
    }
    fprintf(stderr, "Usage: %s [-i filename | --stdin] [-o filename]\n", argv[0]);
    return 1;
  }

  if (in_filename != NULL) {
    FILE * fp = fopen(in_filename, "r");
    if (fp == NULL) {
      return -1;
    }
    state = load_board(fp);
    initialize_snakes(state);
  } else if (io_stdin) {
    state = load_board(stdin);
    initialize_snakes(state);
  } else {
    create_default_state();
  }
  update_state(state, deterministic_food);
  if (out_filename != NULL) {
    FILE *fp = fopen(out_filename, "w+");
    print_board(state, fp);
  } else {
    print_board(state, stdout);
  }
  free_state(state);
  return 0;
}
```

