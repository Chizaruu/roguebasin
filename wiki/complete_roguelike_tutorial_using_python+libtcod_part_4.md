# Complete Roguelike Tutorial, using python+libtcod, part 4

---

This is part of a series of tutorials; the main page can be found here.

The tutorial uses libtcod version 1.6.0 and above.

## Field-of-view and exploration

---

### Field of View (FOV)

---

The next step towards a complete roguelike is FOV. This adds a tactical element, and lets the player wonder what's on the other side of every door and every corner! The FOV works like a light source where the player stands, casting light in every direction but not getting past any walls. Regions in shadow are invisible. You could code it yourself by casting rays outward from the player, but it's much easier than that; libtcod has a whole module dedicated to it! It includes different methods with varying levels of precision, speed and other interesting properties. There's an [excellent study here](comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds.md) if you want to know more about them, including tables and images comparing the different algorithms.

We'll define the chosen algorithm along with some other constants so they can be changed later. For now it's 0, the default algorithm. There's also an option to light walls or not, this is a matter of preference. Another important constant is the maximum radius for FOV calculations, how far the player can see in the dungeon. (Whether this is due to the player's sight range or the light from the player's torch depends on how you choose to explain this to the player.)

```python
FOV_ALGO = 0  #default FOV algorithm
FOV_LIGHT_WALLS = True
TORCH_RADIUS = 10
```

Also, we'll need more colors for lit tiles! The color definitions will now be:

```python
color_dark_wall = libtcod.Color(0, 0, 100)
color_light_wall = libtcod.Color(130, 110, 50)
color_dark_ground = libtcod.Color(50, 50, 150)
color_light_ground = libtcod.Color(200, 180, 50)
```

These are taken straight away from the libtcod sample that comes with the library, and you may want to change them to give your game a more unique feel (see the [earlier notes](complete_roguelike_tutorial,_using_python+libtcod,_part_2.md#The_Map) about colors).

The libtcod FOV module needs to know which tiles block sight. So, we create a map that libtcod can understand (fov_map), and fill it with the appropriate values from the tiles' own block_sight and blocked properties. Well, actually, only block_sight will be used; the blocked value is completely irrelevant for FOV! It will be useful only for the pathfinding module, but it doesn't hurt to provide that value anyway. Also, libtcod asks for values that are the opposite of what we defined, so we toggle them with the not operator. This goes in the body of the script, before entering the main game loop.

```python
fov_map = libtcod.map_new(MAP_WIDTH, MAP_HEIGHT)
for y in range(MAP_HEIGHT):
    for x in range(MAP_WIDTH):
        libtcod.map_set_properties(fov_map, x, y, not map[x][y].block_sight, not map[x][y].blocked)
```

FOV will only need to be recomputed if the player moves, or a tile changes. To model that we'll define a global variable fov_recompute = True before the main loop. Then, in the handle_keys function, whenever the player moves we set it to True again, like in the following code.

```python
    #movement keys
    if libtcod.console_is_key_pressed(libtcod.KEY_UP):
        player.move(0, -1)
        fov_recompute = True

    elif libtcod.console_is_key_pressed(libtcod.KEY_DOWN):
        player.move(0, 1)
        fov_recompute = True

    elif libtcod.console_is_key_pressed(libtcod.KEY_LEFT):
        player.move(-1, 0)
        fov_recompute = True

    elif libtcod.console_is_key_pressed(libtcod.KEY_RIGHT):
        player.move(1, 0)
        fov_recompute = True
```

Now we need to change the rendering code to actually recompute FOV, and display the result! It's a major overhaul of the render_all function. We only need to recompute FOV and render the map if recompute_fov is True (and then we reset it to False), done by the following code.

```python
    if fov_recompute:
        #recompute FOV if needed (the player moved or something)
        fov_recompute = False
        libtcod.map_compute_fov(fov_map, player.x, player.y, TORCH_RADIUS, FOV_LIGHT_WALLS, FOV_ALGO)
```

As you can see we're using all the constants we defined earlier. After that comes the code that iterates over all tiles and displays them in the console. We'll add an extra condition for each tile:

```python
visible = libtcod.map_is_in_fov(fov_map, x, y)
```

Depending on the value of visible, the tile may be drawn in different colors (lit or dark). We'll show all of the modified map display code to make this a bit more clear.

```python
        #go through all tiles, and set their background color according to the FOV
        for y in range(MAP_HEIGHT):
            for x in range(MAP_WIDTH):
                visible = libtcod.map_is_in_fov(fov_map, x, y)
                wall = map[x][y].block_sight
                if not visible:
                    #it's out of the player's FOV
                    if wall:
                        libtcod.console_set_char_background(con, x, y, color_dark_wall, libtcod.BKGND_SET)
                    else:
                        libtcod.console_set_char_background(con, x, y, color_dark_ground, libtcod.BKGND_SET)
                else:
                    #it's visible
                    if wall:
                        libtcod.console_set_char_background(con, x, y, color_light_wall, libtcod.BKGND_SET )
                    else:
                        libtcod.console_set_char_background(con, x, y, color_light_ground, libtcod.BKGND_SET )
```

There, it's done!

The last detail is to make sure objects only show if they're in the player's FOV. In the Object 's draw method, add a FOV check before drawing:

```python
if libtcod.map_is_in_fov(fov_map, self.x, self.y):
```

Apart from defining the newly used global values in render_all and handle_keys (they're fov_map and fov_recompute), that's all there is to it. This is actually one aspect that can take a long time to get right in a roguelike, fortunately we were able to do it with a modest amount of work!

The whole code for this section is [here](complete_roguelike_tutorial_using_python+libtcod_part_4_code.md#field_of_view).

### Exploration

---

The last detail after FOV is exploration, a.k.a Fog of War. You made it this far, so this will be a piece of cake! What, you may say, fog of war can't possibly be the easiest thing to code in a roguelike! Well, it is. Wait and see.

First, all tiles will store whether they're explored or not. They start unexplored. This is in the Tile 's __init__ method.

```python
self.explored = False
```

Now, in the render_all function, after the if not visible: line, add this:

```python
#if it's not visible right now, the player can only see it if it's explored
if map[x][y].explored:
```

And indent the next four lines so they only execute if that's true. So only explored tiles will be drawn.

Then, after rendering a visible tile (right at the end of the function), explore the visible tile:

```python
map[x][y].explored = True
```

And that is all. The level will start black, but you'll slowly uncover it. Explored regions are still visible but are in a different color and won't reveal any objects (such as lurking monsters)! It's an exploration game now.

The whole code is available [here](complete_roguelike_tutorial_using_python+libtcod_part_4_code.md#Exploration).

[Go on to the next part](complete_roguelike_tutorial,_using_python+libtcod,_part_5.md).
