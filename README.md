# SI335 Project: The Battlefield

*   [Overview](#overview)
*   [Reminders](#reminders)
*   [Input and output](#input-and-output)
*   [Example](#example)
*   [How your code will be run](#how-your-code-will-be-run)
*   [Limitations and assumptions](#limitations-and-assumptions)
*   [What to turn in](#what-to-turn-in)
*   [Submitting](#submitting)
*   [Helper programs](#helper-programs)
*   [Languages](#languages)
*   [Downloading this repo](#downloading-this-repo)
*   [Grading](#grading)

## Overview

> *Now the general who wins a battle makes many calculations in his
> temple ere the battle is fought. The general who loses a battle makes
> but few calculations beforehand. Thus do many calculations lead to
> victory, and few calculations to defeat; how much more no calculation
> at all! It is by attention to this point that I can foresee who is
> likely to win or lose.*
>
> ---Sun Tzu, *The Art of War*

In this project, you are going to find optimal or nearly-optimal paths
for some number of *assets* to acquire a number of *targets* in a
battlefield, while avoiding some *obstacles*.

The basic idea is this: The assets each move around the battlefield at
the same time. They are trying to go to each target in order to get
points. Your task is to make a plan for how the assets will move around.

**Moving**: Only the assets move; the targets and obstacles stay in
place. At each step, every asset moves one space either up, down, left,
or right. If an asset moves into a space with a target, you get the
points for that target (see below). If an asset moves into a space with
an obstacle, that asset is gone and any further moves of the asset are
ignored. Same if the asset moves outside of the boundaries. (Nothing
special happens if two assets occupy the same space.)

**Scoring**: Each target starts with a value, which will be some
positive integer. **At each step, the value of every remaining target
goes down by one**. The battle ends when all targets have been acquired,
or all their values have gone down to zero, or all assets have run into
obstacles, or your plan has run out of moves.

Your task is to read in a description of the map (dimensions, obstacles,
assets, and targets) and output a series of moves for all the assets to
take in parallel, in order to acquire the targets as quickly as
possible. The goal is to get the highest score, which is the total value
of all targets at the time you acquire them.


## Reminders

-   Review the
    [relevant section from the course policy](https://usna.edu/Users/cs/roche/335/resources/policy.php#collaboration)
    on honor and allowed collaboration for programming projects.
-   **If you have any doubt about what is allowed, just ask!**
-   Part of your grade will be based on "coding style". This is mostly
    about whether your code is easy to follow, consistently formatted,
    and sensibly designed. You should use meaningful
    variable/function/class names, clearly explain what is going on in
    your code, and "clean up" any extra debugging or non-functional
    parts before submitting.
-   Be sure to follow the **exact input/output specifications and
    filename conventions** as specified. **You should write any error or
    debugging messages to stderr (cerr)**, as this stream will be
    ignored in testing.
-   Your program will automatically compiled and tested by a variety of
    exciting bash scripts. This means that it is imperative that your
    code **compiles without errors** in the CS linux lab environment and
    that it behaves exactly as specified. There will be a few "sanity"
    checks in the submit system that you should take advantage of.

## Input and output

The input to your program will be a single file that stores a "map"
specifying the battlefield dimensions, the number and locations of any
obstacles, the number and locations of any assets, and the number,
location, and *value* of any targets.

Specifically, the input map file consists of four parts:

1.  **Dimensions**: Two integers for the number of rows and columns in
    the battlefield.
2.  **Obstacles**: An integer for the number of obstacles, followed by
    that many pairs of integers for the coordinates of each obstacle.
3.  **Assets**: An integer for the number of assets, followed by that
    many pairs of integers for the starting coordinates of each asset.
4.  **Targets**: An integer for the number of targets, followed by that
    many triples of integers, indicating the row index, column index,
    and *starting value* of each target.

There is a single blank line separating each part.

The output from your program will be in a file specifying the "moves"
that each asset should make in order to reach the targets quickly.
Specifically, your output will consist of a number of lines, one per
step. On each line, there will be *k* directions, where *k* is the
number of assets. Each direction is either `U`, `D`, `L`, or `R`. Note
it is not allowed for an asset to stand still!

## Example

Here is an example map file:

    10 12

    7
    0 6
    3 5
    4 6
    4 7
    4 10
    5 2
    7 3

    2
    0 8
    9 3

    11
    0 9 36
    2 9 95
    4 5 56
    5 9 56
    6 5 88
    6 7 81
    7 6 12
    8 5 76
    9 0 60
    9 4 96
    9 5 74

You can see that the file above is split into 4 parts:

1.  **Dimensions**: The map has 10 rows and 12 columns.
2.  **Obstacles**: The map has 7 obstacles, at the specified
    coordinates.
3.  **Assets**: There are 2 assets, at the specified coordinates.
4.  **Targets**: There are 11 targets, at the specified coordinates.
    Each target also has a specified *starting value*.

A visual representation of this map (which comes from running
`python3 show.py`) is below. The obstacles are
asterisks, the assets are numbers (in green), and the targets are
letters.

**Notice: rows and columns are numbered from 0, starting at the top-left
corner.**

![](map-start.png)

The output from your program will be a list of *moves* for the assets.
For the map above, it might look something like this:

    RL
    DL
    DL
    DR
    DR
    DR
    DR
    LR
    LU
    UU
    UR
    UL
    UU
    UU
    RU

You can see there are a number of lines, and on each line there is one
direction for each of the assets to move in. So the first line above
says asset 1 should move one space to the right, and asset 2 should move
1 space to the left. On step 2, asset 1 changes direction to move down,
and asset 2 keeps moving left. They both keep going in the same
direction on step 3.

Here's what those three steps look like on the map. (You could see this
by running `python3 show.py sample-map.txt sample-moves.txt`.)

![](map-3step.png)

## How your code will be run

When run, your program should prompt first for the map filename,
and then for the moves filename. The first filename is the *input* to
your program, a map file like shown above. The second filename is the
*output* from your program, which your code must create and write to,
which will contain a list of moves like the example given above.

Any other output from running your program will be ignored by the
auto-testing. I will only look at the resulting moves file.

So a run of your program would look like this:

```
$ ./plan
Map: sample-map.txt
Moves: my-output.txt
```

and after this, a new file called `my-output.txt` should be created with
lines having `R`, `L`, `U`, `D` characters to specify the moves.

## Limitations and assumptions

You may assume the map files always fall within the following limits:

1.  Each **dimension** is at least 1 and at most 200.
2.  The number of **assets** is at least 1 and at most 3
3.  The number of **targets** is at most 26.

You can implement your solution in any programming language that I am
able to easily use. (See the Languages section below, and discuss in
advance with Dr. Roche if you want to use something other than the six
languages listed.)

In any case, it must complete
execution and output all the moves **within 30 seconds runtime**. Of
course, it's OK if it's faster than that!

You may assume that all obstacles, targets, and assets start out in
distinct locations. Assets are not allowed to move onto obstacles, but
they can move into the same square as each other if you want to do that
for some reason. Keep in mind, you only get points for a target the
*first time* any asset reaches that target.

## What to turn in

Submit to [submit.moboard.com](https://submit.moboard.com/) under SI335
and assignment name `proj3`. Your submission should contain two files:

*   Your source code, contained in a single file called
    `plan.cpp` or similar (see table below under Languages)

*   A `README.txt` that:

    +   Has your name
    +   Cites (specifically!) any references you used in accordance with
        the collaboration policy
    +   Briefly explains how your program works.
        What are the main ideas that form the basis of how your program
        works?
        What existing algorithms from this class (or another) did you
        draw on for inspiration?

## Helper programs

The following three Python programs should be helpful in testing and
developing your code. All three are included in this git repository:

*   `generate.py`: generates a random map with the specified dimensions
    and number of obstacles, assets, and targets.
*   `show.py`: makes a nice visual "movie" of your
    moves, along with the scores, so you can see what your program is
    producing.
*   `score.py`: does the same, but there's no visual
    component, just the scores. This is mostly useful for when you get into
    the bigger maps that might be too large to fit on the screen visually.

You run each of these programs by typing, for example,
`python3 generate.py`. They all have nice help messages if you run them
with the `-h` flag.

## Languages

Your program must be called `plan` (or `Plan` for java) and can be written
in any programming language as long as I can run it.

Here are the "standard" languages:

Language | Source code filename
-------- | --------------------
C        | `plan.c`
C++      | `plan.cpp`
Java     | `Plan.java` (and your `main` must be in the `Plan` class)
Python3  | `plan.py`
Rust     | `plan.rs`
Haskell  | `plan.hs`

To use one of these languages, just put *all* of your source code into a
file with the given name and submit it.  **You must use the names above
and put all your source code in a single file**.

(Note, for Java, it *is* possible to put multiple `class`es in the same
`.java` file as long as only the class with the `main` method is `public`.)

If you want to use a different language than the ones listed, send me an
email at least a few days in advance.

## Downloading this repo

To download ("clone") this repository, run from a linux command-line:

```bash
git clone https://github.com/si335usna/battlefield.git
```

This will create a directory `battlefield` from wherever you ran that
command, with all of these files. You can `cd` into that directory and
get to work!

Later, if some updates get made to this starter code repository and you
want to copy them to your local copy, you can just run:

```bash
git commit -am "saving my code"
git pull -s recursive -X ours origin main
```

I *strongly encourage* you to save your work using GitHub (or BitBucket
or GitLab or any other git-hosting service). For example, if you create
a free github account with username `myself`, then create a repository
(click the big + sign) called `myrepo`, you can connect that to your
local version by running

```bash
git remote add mine https://github.com/myself/myrepo
git push -u mine main
```

**Note**: Make sure your repository is *private*; otherwise you are
sharing your solutions with the world and probably violating the course
policy.

## Grading

Your program will be tested on a variety of maps ranging from the
smallest possible, with one asset and few obstacles and targets, to the
largest possible 200x200 with three assets, many obstacles, and 26
targets.

Remember, your program has to **finish within 30 seconds** on any input
file. Otherwise, you won't get any points for that map.

After running your plan program, I will use the `score.py` program,
along with the map file, to find out how many points your "moves"
score.

Say your moves score *x* points. After running all of the
submissions, *as well as my sample solutions*, I will also get the maximum
number of points any of them score on that map file, which I will call
*m*. Your grade for that map will be determined from the ratio
*x*/*m* of your points divided by the maximum number of
points for that map. There will be bonus points available *on every
sample map*, if you get the maximum number of points (or close to it).

As usual, besides these auto-tests, your program will also be graded on
the quality of your README file, as well as the overall
readability, documentation, and code organization of your source code,
according to the guidelines [on this
page](https://www.usna.edu/Users/cs/roche/335/resources/codestyle.php).
