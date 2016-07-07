# Ants Vs. SomeBees
![](https://github.com/timkchan/yelp_map/blob/master/icon8080/gp.png?raw=true)

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



##### Unsupervised Learning
Restaurants tend to appear in clusters (e.g. Southside restaurants, Gourmet Ghetto). In this phase, we will devise a way to group together restaurants that are close to each other.

The __k-means algorithm__ is a method for discovering the centers of clusters. It is called an unsupervised learning method because the algorithm is not told what the correct clusters are; it must infer the clusters from the data alone.

The k-means algorithm finds `k` centroids within a dataset that each correspond to a cluster of inputs. To do so, k-means begins by choosing `k` centroids at random, then alternates between the following two steps:

- Group the restaurants into clusters, where each cluster contains all restaurants that are closest to the same centroid.
- Compute a new centroid (average position) for each new cluster.

This [visualization] is a good way to understand how the algorithm works.



#### Supervised Learning
In this phase, we will predict what rating a user would give for a restaurant. We will implement a supervised learning algorithm that attempts to generalize from examples for which the correct rating is known, which are all of the restaurants that the user has already rated. By analyzing a user's past ratings, we can then try to predict what rating the user might give to a new restaurant.

To predict ratings, we will implement __simple least-squares linear regression__, a widely used statistical method that approximates a relationship between some input feature (such as price) and an output value (the rating) with a line. The algorithm takes a sequence of input-output pairs and computes the slope and intercept of the line that minimizes the mean of the squared difference between the line and the outputs.



### 3. Files

Files in this project:

* `abstractions.py`: Data abstractions used in the project
* `recommend.py`: Machine learning algorithms and data processing
* `utils.py`: Utility functions for data processing
* `ucb.py`: Utility functions for CS 61A
* `data`: A directory of Yelp users, restaurants, and reviews
* `users`: A directory of user files
* `visualize`: A directory of tools for drawing the final visualization


### 4. Running (GUI) - Predicting your own ratings

You can use your project to predict your own ratings too!

1. In the `users` directory, you'll see a couple of `.dat` files. Copy one of them and rename the new file to `yourname.dat` (for example, `john.dat`).

2. In the new file (e.g. `john.dat`), you'll see something like the following:
    ```python
    make_user(
        'John DoeNero',     # name
        [                   # reviews
            make_review('Jasmine Thai', 4.0),
            ...
        ]
    )
    ```
    Replace the second line with your name (as a string).
    
3. Replace the existing reviews with reviews of your own! You can get a list of Berkeley restaurants with the following command:
    ```sh
    $ python3 recommend.py -r
    ```

4. Use `recommend.py` to predict ratings for you:
    ```sh
    $ python3 recommend.py -u john -k 2 -p -q Sandwiches
    ```
    (Replace `john` with your name.) Play around with the number of clusters (the `-k` option) and try different queries (with the `-q` option)!
    
### 5. Class Project Site
[here]

[here]: <http://61a-su15-website.github.io/proj/ants/>
[Plants Vs. Zombies]: <http://www.popcap.com/plants-vs-zombies-1>
[Composing Programs]: <http://composingprograms.com/>
[visualization]: <http://tech.nitoyon.com/en/blog/2013/11/07/k-means/>