# Instructions for Agents

## Prime Directives (Most Important)

* Strive for readable, elegant, and minimal solutions (in that order)
    * Note that "minimal" refers to the code (business logic, moving parts, abstractions, etc)
    * Whitespace, comments, documentation do not count against this
* Stay in the project root! Only read or edit files within it unless otherwise instructed
    * The project root is the directory the agent was launched in
* NEVER run things with `sudo` unless given explicit clearance to do so by the user.

## Preferred Languages and Toolchains

### Languages:

* Python (general stuff)
* C++    (complex work/heavy lifting)

### Toolchains:

Always ask before including any dependencies not listed below

#### Python:
* Package Manager:  uv
* GameDev:          pygame
* Web Backend:      flask
* Web Frontend:     htmx (vendor it into the app's static files)
* Database:         sqlite3
* Test Framework:   pytest
* TUI Framework:    textual
* Whitelisted Libs: requests, numpy, matplotlib, pandas, rich, jinja2

#### C++:
* Build System:    GNU Make
* C++ Standard:    C++23
* Python Bindings: nanobind

## New Project First Steps

### Git

Always git init a `main` branch if starting with an empty project

### Python

Always `uv init` when starting with an empty project

## Commit Workflow

* Only commit if you are told to do so
* NEVER push unless given explicit clearance by the user to do so
* If asked to "commit as you go", then make clean atomic commits as you work

## Coding Style

Use this as a checklist while implementing and before wrapping up any work!

When working in a pre-existing codebase, you may deviate to match project conventions.

### Important Stuff

#### Magic numbers are BAD

Name literals whose meaning, unit, or reason for selection isn’t clear at the point of use.

The following literals are an exception (-1, 0, 1, 2)

HTTP status codes are also an exception

Literals inside the definition of a named constant are also an exception (e.g. the `255`s in `WHITE = (255, 255, 255)`)

#### Pokemon exceptions are BAD

DO NOT CATCH THEM ALL!

Catch the narrowest exception that you can actually do something about

BAD:
```
try:
    settings = load_settings(SETTINGS_PATH)
except Exception:
    settings = {}
```

GOOD:
```
try:
    settings = load_settings(SETTINGS_PATH)
except FileNotFoundError:
    settings = DEFAULT_SETTINGS # first run, no settings file yet
```

GOOD:
```
try:
    settings = load_settings(SETTINGS_PATH)
except json.JSONDecodeError as e:
    raise ValueError(f"Settings file {SETTINGS_PATH} is corrupt!") from e
```

Before you try to catch something, determine if it is something we actually want to
recover from, or handle before rethrowing, or if it should be a fatal exception.

### General

#### We do not care or use autoformatters

Yes a lot of the following rules would be undone by autoformatters. We are not using them!

#### Use block comments like the following to break up sections of a file

```
// Includes
//-------------------------
#include <iostream>


// Constants
//-------------------------
constexpr uint32_t NUM_ITERS = 3;


// Enums
//-------------------------
enum class Modes {ON, OFF, STANDBY};


// Functions
//-------------------------
void update(){
    ...
}
```

```
# Imports
#-------------------------
import os

# Functions
#-------------------------
def update():
    ...
```

#### Write code in "paragraphs"

A paragraph of code completes an idea (be granular).

A header comment should try to explain the WHAT and WHY of the paragraph,
in a very concise single line comment. Multi line comments are to be avoided
unless the complexity truly warrants it.

The purpose of these comments/paragraphs is to make the code very
easy to follow and understand in granular chunks by someone auditing
the code as part of a review. Think of it as writing code in the style
of textbook/instructional examples.

Every chunk/paragraph should have that header comment, but trivial functions
(2 lines or less), can omit the paragraph header.

```
# Initialize status before running update
self.status = 'OK'
self.hp     = INIT_HP
self.lvl    = INIT_LVL

# Call update to wrap up init and process the first tick
self.update()

```
Ensure to leave a space after every "paragraph"

Inline comments (used sparingly) are also appropriate to explain the why behind a line of code

```
int search_value = -1; // default -1 sentinel indicates that no entry was found

```

Ensure comments convey intent. Avoid being too literal.

BAD:

```
// Set health to 100
this->health = 100;
```

BETTER:
```
// Init player health before starting a new level
this->health = 100;
```

#### Line things up!

If you are doing multiple back to back assignments, declarations, etc. Line up special characters like `=` and the start of identifiers.

If you are dealing with a super large difference in identifier or type length that would result in needing to respace a lot of shorter
identifiers and types, add a newline before changing alignment.

BAD:
```
// Initialize state of variables
uint32_t foo = 2;
bool done = false;
std::string msg = "hello world";
```

GOOD:
```
// Initialize state of variables
uint32_t    foo  = 2;
bool        done = false;
std::string msg  = "hello world";
```

BAD:
```
// Free-standing Functions
//-------------------------
void foo() const;
void print() const;
const char* status() const;
void update(const std::string& msg);
```

GOOD:
```
// Free-standing Functions
//-------------------------
void        foo()    const;
void        print()  const;
const char* status() const;

void update(const std::string& msg);
```

BAD:
```
# Initialize game state
self.level = Level()
self.player = Player(*self.level.player_start)
self.enemies = [Enemy(r.x, r.y) for r in self.level.enemies]
```

GOOD:
```
# Initialize game state
self.level   = Level()
self.player  = Player(*self.level.player_start)
self.enemies = [Enemy(r.x, r.y) for r in self.level.enemies]
```

BAD:
```
# Color Constants
#-------------------------
WHITE = (255, 255, 255)
BLACK = (20, 20, 30)
BLUE = (112, 190, 255)
```

GOOD:
```
# Color Constants
#-------------------------
WHITE = (255, 255, 255)
BLACK = (20,  20,  30)
BLUE  = (112, 190, 255)
```

BAD:
```
def draw_text(
    surface: pg.Surface,
    font: pg.font.Font,
    text: str,
    position: tuple[int, int],
    color: tuple[int, int, int] = WHITE,
    center: bool = False,
):
    """Draws readable text with a small shadow."""
    ...
```
GOOD:
```
def draw_text(
    surface:   pg.Surface,
    font:      pg.font.Font,
    text:      str,
    position:  tuple[int, int],
    color:     tuple[int, int, int] = WHITE,
    center:    bool = False,
):
    """Draws readable text with a small shadow."""
    ...

#### Use vertical space when trying to avoid long lines of code

Lines must not exceed 120 chars. This applies to comments as well as code.
When the number of args, items in a list/dict, etc would push a line past
that limit, do the following.

Format `()`, `[]`, and `{}` pairs with one item per line and the closing
bracket on its own line, as follows.

BAD:
```
def _add_block(self, col: int, row: int):
    """Adds a solid block at a tile coordinate"""

    # Update our list of solids based on tile coords
    self.solids.append(pg.Rect(col * st.TILE_SIZE + st.TILE_OFFSET_X, row * st.TILE_SIZE + st.TILE_OFFSET_Y, st.TILE_SIZE, st.TILE_SIZE))
```

ALSO BAD:
```
def _add_block(self, col: int, row: int):
    """Adds a solid block at a tile coordinate"""

    # Update our list of solids based on tile coords
    self.solids.append(pg.Rect(col * st.TILE_SIZE + st.TILE_OFFSET_X, row * st.TILE_SIZE + st.TILE_OFFSET_Y,
                               st.TILE_SIZE, st.TILE_SIZE))
```

GOOD:
```
def _add_block(self, col: int, row: int):
    """Adds a solid block at a tile coordinate"""

    # Update our list of solids based on tile coords
    self.solids.append(
        pg.Rect(
            col * st.TILE_SIZE + st.TILE_OFFSET_X,
            row * st.TILE_SIZE + st.TILE_OFFSET_Y,
            st.TILE_SIZE,
            st.TILE_SIZE
        )
    )
```

### Python Specific

#### Avoid using `from blah import foo`

Instead use `import foo` or `import foo as f` if brevity is needed.

Also line things up for imports!

```
import requests as rq
import sqlite3  as sql
```


#### Use type annotations for function args

Always annotate function arguments. Also annotate variables where
understanding the type is critical to understanding the code.

```
def foo(x: int, msg: str):
    ...
```

```
super_complex_thing: SpecialType = x
```

Annotate return types for functions that return a value (`-> int`, `-> str`, etc).

Don't use the `-> None` return annotation. Just omit it for cases where
a function lacks a return, as it clutters the code.

BAD:
```
def foo(x: int, msg: str) -> None:
    ...
```

GOOD:
```
def foo(x: int, msg: str):
    ...
```

#### Brief (single line) docstring for every function/method

```
def send_msg(msg: str):
    """Displays a message to the user"""
    ...
```

NOTE: This does not excuse you from commenting your paragraphs of code
within the function. This serves a different purpose. The purpose of
this docstring is to convey the actions taken by the function to a user
who might not see the function's internal code (via LSP or other tools).

### C++ Specific

#### Follow C++ core guidelines

You can find them here if you are unable to remember them

[C++ Core Guidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md)

Note that our style choices above take higher priority than the C++ CG


#### Use a leading _ for private functions and variables

```
private:
    void _update();
    int  _next_idx;
```

#### Use `this` when referencing member methods and variables within a class method

```
void _take_damage(){
    // Update damage counter then call update so that it takes effect
    this->health--;
    this->update();
}
```
