# Ants Vs. SomeBees
![](https://github.com/timkchan/ants/blob/master/icon8080/gp.png?raw=true)

The file `ant.py` is incomplete (to prevent from plagiarism). If you would like a completed version, please email me at me@timkchan.com

### 1. Introduction
In this project, we will create a __tower defense__ game called __Ants Vs. SomeBees__. As the ant queen, we populate our colony with the bravest ants we can muster. Our ants must protect our queen from the evil bees that invade your territory. Irritate the bees enough by throwing leaves at them, and they will be vanquished. Fail to pester the airborne intruders adequately, and our queen will succumb to the bees' wrath. This game is inspired by PopCap Games' [Plants Vs. Zombies].

### 2. Core Concepts
A game of Ants Vs. SomeBees consists of a series of turns. In each turn, new bees may enter the ant colony. Then, new ants are placed. Finally, all insects (ants, then bees) take individual actions: bees sting ants, and ants throw leaves at bees. The game ends either when a bee reaches the ant queen (we lose), or the entire bee flotilla has been vanquished (we win).

- __The Colony:__ The colony consists of several places that are chained together. The `exit` of each `Place` leads to another `Place`.
- __Placing Ants:__ There are two constraints that limit ant production. Placing an ant uses up some amount of the colony's food, a different amount for each type of ant. Also, only one ant can occupy each `Place`.
- __Bees:__ When it is time to act, a bee either moves to the `exit` of its current `Place` if no ant blocks its path, or stings an ant that blocks its path.
- __Ants:__ Each type of ant takes a different action and requires a different amount of food to place. The two most basic ant types are the `HarvesterAnt`, which adds one food to the colony during each turn, and the `ThrowerAnt`, which throws a leaf at a bee each turn.



### 3. Characters

|                                                         Class                                                        | Food | Armour |                                                                                                                                                                                                           Action                                                                                                                                                                                                          |
|:--------------------------------------------------------------------------------------------------------------------:|:----:|:------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/ant_harvester.gif?raw=true) Harvester |   2  |    1   |                                                                                                                                                                                                        Adds 1 food                                                                                                                                                                                                        |
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/ant_longthrower.gif?raw=true) Long    |   2  |    1   | Only `throw_at` a `Bee` that is found after following at least 5 `entrance` transitions. So the `LongThrower` can't hit `Bees` that are in the same `Place` as it or the first 4 Places in front of it. If there are two `Bees`, one too close to the `LongThrower` and the other within its range, the `LongThrower` should throw past the closer `Bee`, instead targeting the farther one, which is within its range. * |
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/ant_shortthrower.gif?raw=true) Short  |   2  |    1   |                                                                                                                Only `throw_at` a `Bee` that is found after following at most 3 `entrance` transitions. So the `ShortThrower` can only hit `Bee`s in the same `Place` as it and 3 `Places` in front of it. *                                                                                                               |
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/ant_thrower.gif?raw=true) Thrower     |   4  |    1   |                                                                                                                                                                        `throw_at` the nearest `Bee` in front of it that is not still in the `Hive`                                                                                                                                                                        |
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/ant_wall.gif?raw=true) Wall           |   4  |    4   |                                                                                                                                                                      Does nothing each turn. A WallAnt is useful because it has a large armour value.                                                                                                                                                                     |
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/ant_hungry.gif?raw=true) Hungry       |   4  |    1   |                                                                                                                                            Selects a random `Bee` from its `place` and eat it whole. After eating a `Bee`, it must spend 3 turns digesting before eating again                                                                                                                                            |
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/ant_bodyguard.gif?raw=true) Bodyguard |   4  |    2   |            Occupies the same place as another ant. When a `BodyguardAnt` is added to the same place as another ant, it takes the place of the other ant and shields it from damage. Attacks should damage the `BodyguardAnt` first and only hurt the protected ant after the `BodyguardAnt` has run out of armour. Meanwhile the ant inside of a bodyguard should still be able to perform its original action.           |
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/ant_slow.gif?raw=true) Slow           |   4  |    1   |                                                                                                                                                                                             Applies a slow effect for 3 turns.                                                                                                                                                                                            |
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/ant_scuba.gif?raw=true) Scuba         |   5  |    1   |                                                                                                                                              It is water-safe, but otherwise identical to its `Thrower`. A `ScubaThrower` should not lose its armour when placed in `Water`.                                                                                                                                              |
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/ant_ninja.gif?raw=true) Ninja         |   6  |    1   |                                                                                                                                                                                  Damages all `Bee`s that pass by, but can never be stung                                                                                                                                                                                  |
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/ant_fire.gif?raw=true) Fire           |   6  |    1   |                                                                                                                        When the `FireAnt`'s armour reaches zero or lower, it will reduce the armour of all `Bees` in the same `Place` as the `FireAnt` by its `damage` attribute (defaults to `3`).                                                                                                                       |
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/ant_stun.gif?raw=true) Stun           |   6  |    1   |                                                                                                                                                                                             Applies a stun effect for 1 turn.                                                                                                                                                                                             |
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/ant_tank.gif?raw=true) Tank           |   6  |    2   |                                                                                                                                                Same as `BodyGuard` and also fending off `bee`s on its own, on each turn will deal `1` `damage` to all `bee`s in its `place`                                                                                                                                               |
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/ant_queen.gif?raw=true) Queen         |   6  |    1   |                   The `QueenAnt` is a waterproof `ScubaThrower` that inspires her fellow ants through her bravery. The `QueenAnt` doubles the `damage` of all the ants with or behind her, updating any ants whose `damage` hasn't been buffed yet each time she performs an `action`. Once an ant's `damage` has been doubled, it should not be buffed again on subsequent turns. (Hidden Rules Apply)                   |
| ![Image of HarvesterAnt](https://github.com/timkchan/ants/blob/master/icon8080/bee.gif?raw=true) Bee                 |   -  |    ?   |                                                                                                                                                                                                           Enemy!                                                                                                                                                                                                          |
\* Neither of these specialised throwers can `throw_at` a `Bee` that is exactly 4 `Places` away. Placing a single one of these (and no other ants) should never win a default game.

### 4. Files

Files in this project:

* `ants.py`: The game logic of Ants Vs. SomeBees
* `ants_gui.py`: An older GUI for Ants Vs. SomeBees
* `gui.py`: An new GUI for Ants Vs. SomeBees
* `graphics.py`: Utilities for displaying simple two-dimensional animations
* `state.py`: Abstraction for gamestate for `gui.py`
* `utils.py`: Some functions to facilitate the game interface
* `ucb.py`: Utility functions for CS 61A
* `assets`: A directory of images and files used by `gui.py`
* `img`: A directory of images used by `ants_gui.py`
* `icon8080`: A directory of images used by `README.md`


### 5. Running the game

There are 3 ways to run the game:

1. text-based
    ```sh
    $ python3 ants.py
    ```

2. Python GUI
    ```sh
    $ python3 ants_gui.py
    ```

3. Web-based GUI (recommended)
    ```sh
    $ python3 gui.py
    ```

All the methods above follow the same arguments:

    usage: gui.py [-h] [-d DIFFICULTY] [-w] [--food FOOD]
    
    Play Ants vs. SomeBees
    
    optional arguments:
      -h, --help     show this help message and exit
      -d DIFFICULTY  sets difficulty of game (easy/medium/hard/insane)
      -w, --water    loads a full layout with water
      --food FOOD    number of food to start with when testing

e.g. 
```sh
$ python3 gui.py -d hard -w
```

### 6. Class Project Site
[here]

[here]: <http://61a-su15-website.github.io/proj/ants/>
[Plants Vs. Zombies]: <http://www.popcap.com/plants-vs-zombies-1>