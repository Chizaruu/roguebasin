# Scrolling map

---

![Scrolling_map_image](./assets/images/articles/scrolling-map/512px-Camera_vectors.png)

_If we know that D is in world-space at (5, 6) then we can subtract the camera position at (3, 2) to get its position in screen-space at (2, 4). For things we know the position of in screen-space we can do the inverse and add the camera position to get the world-space position. The camera is placed on a world-space position about half a screen away from the players world-space position, which causes the screen-space area to be centered around the player._

A **scrolling map** is a term used in graphical 2D games, including roguelikes, for a map with a "camera" that follows the player. It is used for maps that are larger than that can be displayed on a single screen.

Implementing scrolling maps is mathematically very simple. Let s be the dimensions of the screen, p be the player coordinates, and c be the coordinates of the upper left of the camera:

ㅤ*c* := _p_ - (_s_ / 2)

All this means is that the camera location is the same as the player location, except offset by half the screen dimensions so that the player is nicely centered in the viewport.

If the player is near the edge of the map, then the blank area outside the map might become visible. If you don't want this area to be displayed, and instead have the camera dock to the edge of the map, then the formula is revised to this, where m is the map size:

ㅤIf _p_ < _s_ / 2, then _c_ := 0.  
ㅤIf _p_ >= _m_ - (_s_ / 2), then _c_ := _m_ - _s_.  
ㅤOtherwise, _c_ := _p_ - (_s_ / 2).

Apply this procedure to each of the individual coordinates—first the x coordinates, and then the y ones.

The formula looks rather complicated, but all it says is this:

- If the player is near an edge, push the camera against that edge.
- Otherwise, center the camera on the player.

## Implementation

---

### Python

Here's a handy utility function I use often for scrolling maps in Python:

```python
# (Public domain code)

def scrolling_map(p: int, hs: int, s: int, m: int) -> int:
    """
    Get the position of the camera in a scrolling map:

     - p is the position of the player.
     - hs is half of the screen size, and s is the full screen size.
     - m is the size of the map.
    """
    if p < hs:
        return 0
    elif p >= m - hs:
        return m - s
    else:
        return p - hs
```

### C

[Here's a link to C# implementation.](http://www.evilscience.co.uk/?p=587)
