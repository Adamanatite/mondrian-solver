# Beam Search Algorithm for Solving the Mondrian Tiling Problem

## Problem
The [Mondrian tiling problem](https://demonstrations.wolfram.com/MondrianArtProblem/) is an optimisation problem where given an n x n square, the aim is to divide the square into non-congruent rectangles in a way that minimises the difference between the area of the largest and smallest rectangle. For this assignment, we had to design and implement an agent to solve this problem for any given n, including the design and implementation of the problem states and actions and a visual representation of the agent's solution for a given tiling. This was an assignment for a 4th year university course.

## States and Actions
I chose to design the states and actions as follows:

### States
A state in my implementation is a partially tiled grid. As attributes it has its board size a
(initialised at the start and inherited from parent states) and a set of non-overlapping
integer-sized rectangles r=(rx,ry,rw,rh) where (rx,ry) are co-ordinates of the bottom right
corner of the rectangle, and rw and rh are the rectangle width and height respectively. It also
has a signature s, which is the list of sorted areas of each rectangle on the board. This is
used to compare to other states to ensure we don’t explore the same state twice in the
search algorithm. The initial state has one rectangle (0, 0, a, a)

### Actions

I have defined two different types of actions to reduce the largest area and increase the
smallest area of the rectangles, respectively: **Box cutting** and **merging.**

#### Box cutting:
Choose the largest rectangle by area, rmax (if it is not unique, an rmax is chosen arbitrarily
from the possible values) and perform a split (x1, y1, x1, y2) where x1,y1,x2,y2 are integers
\>=0, with constraints x1 < x2 <= rmaxh, y1 < y2 <= rmaxw. This split is performed by first
splitting the rectangle horizontally at height x1, then the newly created bottom region
vertically at width y1, then the new right region horizontally at height x2, then the new top
region vertically at width y2. Any new rectangles with 0 area are discarded. Note if (x1, x2) =
(0, rmaxh) or (y1,y2) =(0, rmaxw) then this is a purely horizontal or vertical split respectively.
>
#### Merging:
Choose any two rectangles which share a non-opposite corner (e.g. they share at least part
of an edge at a corner). For the larger of the two rectangles, split off the area at this shared
edge section (cut off a rectangle whose dimensions are the length of the shared edge on its
axis, and the length of the larger rectangle on the other) and add it to the smaller rectangle.
If these rectangles share a whole edge, this will merge the two rectangles into one (the area
0 rectangle is discarded). If they do not, the merge is only generated if the smaller rectangle
is shorter than the larger one in the axis of the shared edge.

## Solving Algorithm
The state-space search method I chose to use was **beam search** (modified best first search).

### How it works
The beam search algorithm works as follows:
* Maintain a set of previously seen states
* Add the initial state (an empty square) to the beam for the first iteration:
* Try all the possible next actions (box cuts and merges) from each state in the beam
* Sort all of the new and legal states produced by these actions in order of best score. Maintain the best state seen in the overall search.
* Select the best M new states and add them to the beam for next iteration
* Repeat the previous 3 steps until we have reached the maximum defined depth.
* At the end, return the best state seen in the search.

### Limiting parameter
The limiting parameter M I used is the beam size of the beam search (how many
solutions do we search at every step). Another possible M could be depth (how many layers
deep do we go to find the best solutions) while fixing the beam size. I found that increasing
depth past a certain point made little difference to the solutions found, so I instead chose to
limit by beam size and fix depth=10.


## Effectiveness
Shown below is a table of best Mondrian score found for different values of a (board size)
and M (beam size) using the beam search, as well as the optimal scores from [Wolfram](https://demonstrations.wolfram.com/MondrianArtProblem/):

| M value    | a=8 | a=12 | a=16 | a=20 |
| -------- | ------- | ------- | ------- | ------- |
| 1  | 10    | 8 | 18 | 13 |
| 10 | 6     | 8 | 15 | 13 |
| 25    | 6    | 8 | 12 | 13 |
| 50    | 6    | 8 | 11 | 13 |
| **Optimal score:**    | **6**    | **7** | **8** | **9** |

