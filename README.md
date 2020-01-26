# Killer Devouring Snake

This project main purpose is to find the best implementation of an artificial player that maximizes the scores of the famous [Snake][wiki-snake] video game.

You can find a working demo of it [here][demo].

## Rules

There are many variations of the game, some with multiple fruits, snakes, etc. Here, the rules are as follow:
- board size is 16x16;
- snake starts with a size of 1 at random position but at least 4 cells from the border and with a random direction;
- snake can only move horizontally and vertically and cannot turn back;
- snake grows 1 in size whenever it eats a fruit;
- fruit spawns at random empty location and stays there until it is eaten;
- there can be only one fruit at a time (except when the snake fills the board in which case the game is won).

## Scoring

The application will run a new game based on the previous rules everytime a game has finished (win or lose).
The goals of this AI are the following by order of priority:
1. win as many times as possible;
2. if winning is not an option, make the snake grow as big as possible before it loses;
3. achieve the previous with as little moves as possible.

For that, we track the run number, mean score, mean move count and win rate.

## Algorithms

The most obvious and quickest solution would be to compute the shortest path to the fruit everytime it is eaten. There are many way to implement that. The way it is done here is based on the [breadth-first search algorithm][wiki-bfs].
Though this method has the advantage of minimizing the move count, it performs poorly regarding the win rate and the score since it will make the snake eventually run into its tail.

One way to ensure that the snake will never trap itself is to find a circuit that goes through all the cells of the board but only once. This is known as a [Hamiltonian cycle][wiki-hamilton]. To implement it, we first compute the longest path from the head of the snake to its tail end, then we join the tail end to the head. Our longest path implementation here works by extending the shortest path until all the board cells are visited exactly one time.
With this, we are guaranteed to win the game everytime.

There is one major problem with the Hamiltonian cycle though: it takes a very long time for the snake to fill the board. That's because it just repeats following the same circuit without even caring where the fruits pop up (it will eventually run into them anyway). Hence, the real challenge here is to lower the number of moves without impacting the main goals (ideally keeping the win rate at 100%).

This is the solution we came up with:
1. go for a direct shortest path to the fruit (try several different ones) on the condition that we are able to recompute a Hamiltonian cycle based on the projected resulting snake;
2. if 1. is not possible, try a shortcut without "breaking" the Hamiltonian cycle by following these constraints:
    - every projected next cell must have a higher Hamiltonian index than the previous (except when the previous index is 255);
    - the snake head cannot overlap the tail nor pass through Hamiltonian indices that are between the previous head and tail end positions;
3. if 2. is not possible either, just follow the initial Hamiltonian cycle.

With that we achieved about 75% gain on the move count vs a basic Hamiltonian cycle without loosing a single game.

[demo]: https://xmamat.github.io/killer-devouring-snake/
[wiki-snake]: https://en.wikipedia.org/wiki/Snake_(video_game)
[wiki-bfs]: https://en.wikipedia.org/wiki/Breadth-first_search
[wiki-hamilton]: https://en.wikipedia.org/wiki/Hamiltonian_path