# Comparative study of field of view algorithms for 2D grid based worlds

---

```text
    v1.2
    Jice, February 2009
    (The original FOW study in a PDF located [here](http://roguecentral.org/doryen/data/libtcod/fov.pdf).)
```

## Introduction

---

Computing field of view is a frequent problem in video games. It consists of the determination of which part of the world is visible from a certain position. This paper focus on 2D grid based worlds, in other words, worlds represented by a 2D grid of square cells. This type of worlds can be found in a range of video games, including but not limited to 2D isometric games and text console based games like roguelikes.

While there are lots of different algorithms to solve this problem, not much has been written about the difference between those algorithms, in term of gameplay or speed.

This is not a comprehensive study of all available algorithms. Instead, I will focus on following popular or innovative algorithms:

- basic raycasting
- diamond raycasting
- [recursive shadowcasting](fov_using_recursive_shadowcasting.md)
- [precise permissive fov](precise_permissive_field_of_view.md)
- [digital fov](digital_field_of_view.md)

Note that basic raycasting alone generates too many artifacts to be really usable. The algorithm tested here use a post- processing step to remove most wall lighting artifacts.

In this version of the study, permissive has been replaced by an enhanced version of the algorithm, from the same author, with a variable permissiveness parameter. This parameter can take values in the range 0-8, 0 being the less permissive and 8 being equivalent to the standard precise permissive algorithm.

All tests below are done using the C++ wrapper for the [Doryen library](doryen_library.md) and the fov algorithms implementations it contains. Note that digital fov is no more in the library, but its source code is still on libtcod's svn repository.

## Gameplay study

---

### Pillar behavior

We call a wall cell surrounded with empty cells a pillar. First, let's see how the fov looks when the viewer's cell is adjacent to the pillar:

<table>
    <tbody>
        <tr>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/nbPg0HW.png" alt="nbPg0HW.png"></td>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/ISxP0rz.png" alt="ISxP0rz.png"></td>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/RoIuMk8.png" alt="RoIuMk8.png"></td>
        </tr>
        <tr>
            <td>BASIC</td>
            <td>SHADOW</td>
            <td>DIAMOND, DIGITAL</td>
        </tr>
    </tbody>
</table>
<table>
    <tbody>
        <tr>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/uWcXLBc.png" alt="uWcXLBc.png"></td>
        </tr>
        <tr>
            <td>PERMISSIVE</td>
        </tr>
    </tbody>
</table>

DIAMOND and DIGITAL result in a shadow limited to a single line, which makes it almost impossible for a creature to sneak from the east side of the map to the @ position without being noticed.

PERMISSIVE offers any shadow angle. In particular, PERMISSIVE0 has the same shadow angle as SHADOW.

PERMISSIVE2 the same as BASIC.

Now let's see the behavior when the viewer is a few cells away from the pillar:

<table>
    <tbody>
        <tr>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/bvpyzdZ.png" alt="bvpyzdZ.png"></td>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/RrmejbD.png" alt="RrmejbD.png"></td>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/juU50iQ.png" alt="juU50iQ.png"></td>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/4iKeLJe.png" alt="4iKeLJe.png"></td>
        </tr>
        <tr>
            <td>BASIC</td>
            <td>DIAMOND</td>
            <td>SHADOW</td>
            <td>DIGITAL</td>
        </tr>
    </tbody>
</table>
<table>
    <tbody>
        <tr>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/Of5fJRV.png" alt="Of5fJRV.png"></td>
        </tr>
        <tr>
            <td>PERMISSIVE</td>
        </tr>
    </tbody>
</table>

BASIC and SHADOW still have a triangular shadow, but the cells near the pillar are now completely in the field of view.

DIAMOND still has a shadow limited to a single line.

DIGITAL don't even have a line shadow.

Once gain PERMISSIVE is very similar to SHADOW/BASIC with low permissiveness and is equivalent to DIGITAL with maximum permissiveness.

The conclusion is that if you want to use pillars for stealth gameplay, you have to choose either BASIC or SHADOW or PERMISSIVE with low permissiveness. You can still use the other algorithms provided you use 2x2 pillars instead of single cell pillars. Note that no algorithm provides a good looking shadow.

### Corner peeking

Corner peeking involves seeing a corridor when you're standing at a T junction.

<table>
    <tbody>
        <tr>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/Ca1mMam.png" alt="Ca1mMam.png"></td>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/NJRQYqd.png" alt="NJRQYqd.png"></td>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/o6NzrvC.png" alt="o6NzrvC.png"></td>
        </tr>
        <tr>
            <td>BASIC</td>
            <td>SHADOW</td>
            <td>DIAMOND, DIGITAL</td>
        </tr>
    </tbody>
</table>
<table>
    <tbody>
        <tr>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/Q0Frrj4.png" alt="Q0Frrj4.png"></td>
        </tr>
        <tr>
            <td>PERMISSIVE</td>
        </tr>
    </tbody>
</table>

BASIC and SHADOW don't allow corner peeking. You have to step into the corridor to be able to see it. The other algorithms allow corner peeking.

Now let's see the opposite case. If you're in the corridor, will you be able to see someone hiding in the T junction ? When on a red cell, you don't see him. When on a blue cell, you see him.

<table>
    <tbody>
        <tr>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/eke9P2U.png" alt="eke9P2U.png"></td>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/JcL7dYr.png" alt="JcL7dYr.png"></td>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/10ai7BP.png" alt="10ai7BP.png"></td>
        </tr>
        <tr>
            <td>BASIC</td>
            <td>DIAMOND</td>
            <td>SHADOW, PERMISSIVE, DIGITAL</td>
        </tr>
    </tbody>
</table>

For BASIC, the cell might or might not be visible, depending on the viewer position in the corridor. Most of the time, it's not in fov, but at certain position where a ray pass exactly through the T junction cell, it is in fov. This is clearly not an acceptable behavior.

DIAMOND has the better behavior here: the cell is not visible until the viewer is really close to the junction.

For other algorithms, the cell is always visible, which is counter-intuitive, but acceptable.

### Diagonal walls

Most roguelikes allow diagonal movements. We can then expect the field of view to go through diagonal walls too.

<table>
    <tbody>
        <tr>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/WwFlsvX.png" alt="WwFlsvX.png"></td>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/kph1XmO.png" alt="kph1XmO.png"></td>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/XUCC8Ur.png" alt="XUCC8Ur.png"></td>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/2r8vtZ4.png" alt="2r8vtZ4.png"></td>
        </tr>
        <tr>
            <td>BASIC</td>
            <td>DIAMOND</td>
            <td>SHADOW</td>
            <td>DIGITAL</td>
        </tr>
    </tbody>
</table>
<table>
    <tbody>
        <tr>
            <td><img src="./assets/images/articles/comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds/6j5DtLN.png" alt="6j5DtLN.png"></td>
        </tr>
        <tr>
            <td>PERMISSIVE</td>
        </tr>
    </tbody>
</table>

BASIC and some of the PERMISSIVE have good result.

DIAMOND completely blocks the field of view, which might be a real issue if your game allows diagonal movement. Also note that if you want the field of view to be blocked by diagonal walls, you'll have to tweak any algorithm except DIAMOND and PERMISSIVE0.

SHADOW through diagonal walls is limited to a thick line, which is not very natural.

PERMISSIVE8 and DIGITAL have a 90Â° field of view through the wall, which is fine.

### Symmetry

The measures are done on following maps:

- ????Outdoor???? maps (empty maps with random 1x1 obstacles): 100x100
- ????Indoor???? maps (random cave levels using BSP algorithm[5]): 40x40

Symmetry is measured by calculating a field of view F0 on a random map from a random position P0. Then, for each cell Pi in F0, we calculate the field of view Fi from the position Pi and check that P0 is inside Fi. If not, we increase an error counter. We do this for several random maps and get the average number of error per map cell.

Green cells have no symmetry error.

Yellow cells have less than 1% symmetry errors.

Red cells have more than 1% symmetry errors.
Algorithm | Error / cell â€“ indoor (%) | Error / cell â€“ outdoor (%)
---|---|---
BASIC | 0.75 | 4.4
SHADOW | 0.93 | 6.9
DIAMOND | 0.82 | 6.5
PERMISSIVE 0 | 2.14 | 10.95
PERMISSIVE 1 | 2.06 | 10.5
PERMISSIVE 2 | 1.9 | 9.65
PERMISSIVE 3 | 1.39 | 8.1
PERMISSIVE 4 | 1.12 | 7
PERMISSIVE 5 | 0.84 | 4.1
PERMISSIVE 6 | 0.61 | 2.25
PERMISSIVE 7 | 0.16 | 0.3
PERMISSIVE 8 | 0 | 0
DIGITAL | 0 | 0

Conclusion:

- Obviously, symmetric algorithms have no errors.
- On permissive, symmetry is inversely proportional to permissiveness. The symmetry is really bad with permissiveness <= 4.
- All other algorithms have equivalent error rates, acceptable for indoor, but not for outdoor. The outdoor error rate is high enough to be a gameplay issue.
- Error rate is higher in outdoor maps

### Gameplay summary

| Algorithm   | 1x1 pillar near | 1x1 pillar away | Corner peeking | Inverted corner peeking | Diagonal walls | Symmetry indoor | Symmetry outdoor |
| ----------- | --------------- | --------------- | -------------- | ----------------------- | -------------- | --------------- | ---------------- |
| BASIC       |
| DIAMOND     |
| SHADOW      |
| PERMISSIVE0 |
| PERMISSIVE1 |
| PERMISSIVE2 |
| PERMISSIVE3 |
| PERMISSIVE4 |
| PERMISSIVE5 |
| PERMISSIVE6 |
| PERMISSIVE7 |
| PERMISSIVE8 |
| DIGITAL     |

Gameplay ranking:
Algorithm | green | yellow | red
---|---|---|---
BASIC | 3 | 2 | 2
PERMISSIVE3 | 3 | 2 | 2
PERMISSIVE2 | 3 | 2 | 2
PERMISSIVE1 | 3 | 2 | 2
PERMISSIVE8 | 3 | 1 | 3
DIGITAL | 3 | 1 | 3
SHADOW | 2 | 3 | 2
PERMISSIVE0 | 2 | 3 | 2
DIAMOND | 1 | 3 | 3
PERMISSIVE7 | 1 | 3 | 3
PERMISSIVE6 | 1 | 4 | 2
PERMISSIVE5 | 1 | 4 | 2
PERMISSIVE4 | 1 | 4 | 2

Conclusions:

- there is no perfect algorithm amongst the ones observed
- each algorithm has its own weakness
- the resulting ranking is rather arbitrary. You should carefully check every algorithm features to see if it can fit your game.

## Performance study

---

The measures are done on following maps:

- Empty maps (worst case): 600x600, 100x100, 20x20
- Maps full of wall (best case): 600x600, 100x100, 20x20
- ????Outdoor???? maps (empty maps with random 1x1 obstacles): 600x600, 100x100, 20x20
- ????Indoor???? maps (random cave levels using BSP algorithm[5]): 80x80, 40x40, 20x20

For each map type, we run 50 tests on 50 different random maps (for the worst/best cases, there's only one map for all 50 tests). For each random map, we run a number (between 10 and 2000 depending on the current test's average speed) of fov computations from different positions in the map. Each algorithm runs through the exact same set of maps/viewer positions. The cumulative time is calculated for each algorithm and the average time per computation is deduced.

The absolute speed values are not really significant. More important is the difference of speed between two algorithms on the same map and the same computer.

The color code uses following convention:

- algorithms with total time lower than 2x the fastest are green
- algorithms with total time higher than 5x the fastest are red
- the others are yellow

| Empty map    | 600x600 (Âµs) | 100x100 (Âµs) | 20x20 (Âµs) |
| ------------ | ------------- | ------------- | ----------- |
| BASIC        | 29000         | 589           | 35          |
| SHADOW       | 16000         | 383           | 30          |
| DIAMOND      | 91000         | 925           | 58          |
| PERMISSIVE 0 | 27439         | 606           | 37          |
| PERMISSIVE 1 | 27039         | 604           | 36          |
| PERMISSIVE 2 | 27399         | 607           | 37          |
| PERMISSIVE 3 | 27160         | 600           | 37          |
| PERMISSIVE 4 | 27140         | 618           | 37          |
| PERMISSIVE 5 | 26900         | 607           | 36          |
| PERMISSIVE 6 | 26879         | 585           | 36          |
| PERMISSIVE 7 | 27059         | 606           | 37          |
| PERMISSIVE 8 | 26980         | 606           | 37          |
| DIGITAL      | 148000        | 3958          | 166         |

| Full map     | 600x600 (Âµs) | 100x100 (Âµs) | 20x20 (Âµs) |
| ------------ | ------------- | ------------- | ----------- |
| BASIC        | 2507          | 101           | 13          |
| SHADOW       | 485           | 21            | 3           |
| DIAMOND      | 6086          | 155           | 11          |
| PERMISSIVE 0 | 893           | 111           | 10          |
| PERMISSIVE 1 | 736           | 113           | 10          |
| PERMISSIVE 2 | 731           | 110           | 10          |
| PERMISSIVE 3 | 721           | 111           | 10          |
| PERMISSIVE 4 | 750           | 111           | 10          |
| PERMISSIVE 5 | 731           | 112           | 10          |
| PERMISSIVE 6 | 726           | 111           | 10          |
| PERMISSIVE 7 | 736           | 111           | 10          |
| PERMISSIVE 8 | 747           | 111           | 11          |
| DIGITAL      | 639           | 106           | 22          |

| Outdoor map  | 600x600 (Âµs) | 100x100 (Âµs) | 20x20 (Âµs) |
| ------------ | ------------- | ------------- | ----------- |
| BASIC        | 9000          | 242           | 32          |
| SHADOW       | 48000         | 309           | 35          |
| DIAMOND      | 19000         | 318           | 48          |
| PERMISSIVE 0 | 9989          | 280           | 34          |
| PERMISSIVE 1 | 10777         | 303           | 36          |
| PERMISSIVE 2 | 11594         | 320           | 36          |
| PERMISSIVE 3 | 12093         | 336           | 38          |
| PERMISSIVE 4 | 12564         | 338           | 38          |
| PERMISSIVE 5 | 13043         | 354           | 39          |
| PERMISSIVE 6 | 13621         | 358           | 40          |
| PERMISSIVE 7 | 14133         | 367           | 40          |
| PERMISSIVE 8 | 14563         | 375           | 41          |
| DIGITAL      | 206000        | 4255          | 272         |

| Indoor map   | 80x80 (Âµs) | 40x40 (Âµs) | 20x20 (Âµs) |
| ------------ | ----------- | ----------- | ----------- |
| BASIC        | 93          | 51          | 26          |
| SHADOW       | 38          | 32          | 21          |
| DIAMOND      | 130         | 67          | 40          |
| PERMISSIVE 0 | 99          | 53          | 31          |
| PERMISSIVE 1 | 102         | 55          | 32          |
| PERMISSIVE 2 | 104         | 56          | 32          |
| PERMISSIVE 3 | 104         | 56          | 33          |
| PERMISSIVE 4 | 103         | 58          | 33          |
| PERMISSIVE 5 | 105         | 58          | 35          |
| PERMISSIVE 6 | 106         | 59          | 33          |
| PERMISSIVE 7 | 107         | 59          | 36          |
| PERMISSIVE 8 | 107         | 60          | 36          |
| DIGITAL      | 440         | 277         | 135         |

Speed ranking:
Algorithm | green | yellow | red
---|---|---|---
SHADOW | 11 | 0 | 1
PERMISSIVE | 8 | 3 | 1
BASIC | 8 | 3 | 1
DIAMOND | 3 | 6 | 3
DIGITAL | 1 | 1 | 10

Conclusions:

- With usual visible map size in games (between 20x20 and 40x40), any algorithm but DIGITAL is fast enough for most usages.
- SHADOW is the fastest on indoor maps and the best overall choice for performances.
- BASIC is the fastest on outdoor maps
- DIGITAL is way slower than the others.

## Summary

---

<table>
  <tr>
    <th style="text-align: center">Algorithm</th>
    <th colspan=3 style="text-align: center">Gameplay</th>
    <th colspan=3 style="text-align: center">Speed</th>
    <th colspan=2 style="text-align: center">Complexity</th>
  </tr>
  <tr>
    <td></td>
    <td>green</td>
    <td>yellow</td>
    <td>red</td>
    <td>green</td>
    <td>yellow</td>
    <td>red</td>
    <td>To understand</td>
    <td>To implement</td>
  </tr>
  <tr>
    <td>BASIC</td>
    <td>3</td>
    <td>2</td>
    <td>2</td>
    <td>8</td>
    <td>3</td>
    <td>1</td>
    <td>*</td>
    <td>*</td>
  </tr>
  <tr>
    <td>PERMISSIVE3</td>
    <td>3</td>
    <td>2</td>
    <td>2</td>
    <td>8</td>
    <td>3</td>
    <td>1</td>
    <td>**</td>
    <td>***</td>
  </tr>
  <tr>
    <td>PERMISSIVE2</td>
    <td>3</td>
    <td>2</td>
    <td>2</td>
    <td>8</td>
    <td>3</td>
    <td>1</td>
    <td>**</td>
    <td>***</td>
  </tr>
  <tr>
    <td>PERMISSIVE1</td>
    <td>3</td>
    <td>2</td>
    <td>2</td>
    <td>8</td>
    <td>3</td>
    <td>1</td>
    <td>**</td>
    <td>***</td>
  </tr>
  <tr>
    <td>PERMISSIVE8</td>
    <td>3</td>
    <td>1</td>
    <td>3</td>
    <td>8</td>
    <td>3</td>
    <td>1</td>
    <td>**</td>
    <td>***</td>
  </tr>
  <tr>
    <td>DIGITAL</td>
    <td>3</td>
    <td>1</td>
    <td>3</td>
    <td>1</td>
    <td>1</td>
    <td>10</td>
    <td>***</td>
    <td>***</td>
  </tr>
  <tr>
    <td>SHADOW</td>
    <td>2</td>
    <td>3</td>
    <td>2</td>
    <td>11</td>
    <td>0</td>
    <td>1</td>
    <td>*</td>
    <td>*</td>
  </tr>
  <tr>
    <td>PERMISSIVE0</td>
    <td>2</td>
    <td>3</td>
    <td>2</td>
    <td>8</td>
    <td>3</td>
    <td>1</td>
    <td>**</td>
    <td>***</td>
  </tr>
  <tr>
    <td>DIAMOND</td>
    <td>1</td>
    <td>3</td>
    <td>3</td>
    <td>3</td>
    <td>6</td>
    <td>3</td>
    <td>**</td>
    <td>**</td>
  </tr>
  <tr>
    <td>PERMISSIVE7</td>
    <td>1</td>
    <td>3</td>
    <td>3</td>
    <td>8</td>
    <td>3</td>
    <td>1</td>
    <td>**</td>
    <td>***</td>
  </tr>
  <tr>
    <td>PERMISSIVE6</td>
    <td>1</td>
    <td>4</td>
    <td>2</td>
    <td>8</td>
    <td>3</td>
    <td>1</td>
    <td>**</td>
    <td>***</td>
  </tr>
  <tr>
    <td>PERMISSIVE5</td>
    <td>1</td>
    <td>4</td>
    <td>2</td>
    <td>8</td>
    <td>3</td>
    <td>1</td>
    <td>**</td>
    <td>***</td>
  </tr>
  <tr>
    <td>PERMISSIVE4</td>
    <td>1</td>
    <td>4</td>
    <td>2</td>
    <td>8</td>
    <td>3</td>
    <td>1</td>
    <td>**</td>
    <td>***</td>
  </tr>
</table>

Conclusions:

- There is no big winner, but SHADOW and BASIC are particularly adapted to most FOV usages except if symmetry is mandatory. They also happen to be the simplest to understand and implement.
- The new permissive fov is pretty handy and can adapt to any usage, but no permissiveness level gives perfect results and the symmetry gets really bad for low permissiveness.
- While having a reputation for being the slowest, BASIC is indeed one of the fastest.
- While it does not rank very well in this study, DIAMOND has some very interesting and unique features and it's definitely worth digging more into it to see if it can be improved.
- The final conclusion is that there is still lot of room for improvement in FOV algorithms, especially on the gameplay side...

## References

---

1. [Piece of cake visibility determination algorithm](https://sites.google.com/site/jicenospam/visibilitydetermination), Jice, Sep 2007.
2. [Modeling Rays for Line of Sight in an Object-Rich World](https://web.archive.org/web/20200324121451/http://www.oocities.org/temerra/los_rays.html).
3. [FOV using recursive shadowcasting](fov_using_recursive_shadowcasting.md), Björn Bergström, 2001.
4. [Precise Permissive Field of View](precise_permissive_field_of_view.md), Jonathon Duerig. [Enhanced version](http://groups.google.com/group/rec.games.roguelike.development/msg/b77fe6999651d023).
5. [Basic dungeon generation](http://roguecentral.org/doryen/articles/bsp-dungeon-generation/), Jice, Sep 2007.
