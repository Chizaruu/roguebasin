# Ruby shadowcasting implementation

---

This is a Ruby implementation of Bjorn Bergstrom's [recursive shadowcasting FOV algorithm](fov_using_recursive_shadowcasting.md). It is basically a straight port of the [Python version](python_shadowcasting_implementation.md), implemented as a module, and could probably do with some optimisation.

To use the module, create a Map class or somesuch and provide two methods:

blocked?(x, y) returns true if the tile at (x, y) blocks view of tiles beyond it (e.g. walls)
light(x, y) marks (x, y) as visible to the player (e.g. lit up on screen)

Then include ShadowcastingFieldOfView within your Map class (or call extend(ShadowcastingFieldOfView) on your Map instance if you want to be dynamic)

On both Ruby 1.8 and 1.9, shadowcasting runs slightly faster than [Precise Permissive FOV](ruby_precise_permissive_FOV_implementation.md) and boasts a circular FOV shape, 8-directions of FOV casting, and is easier to understand. On the other hand, it has artifacts and is not symmetric ... but it is the more popular roguelike algorithm.

```ruby
module ShadowcastingFieldOfView
    # Multipliers for transforming coordinates into other octants
    @@mult = [
                [1,  0,  0, -1, -1,  0,  0,  1],
                [0,  1, -1,  0,  0, -1,  1,  0],
                [0,  1,  1,  0,  0, -1, -1,  0],
                [1,  0,  0,  1, -1,  0,  0, -1],
             ]

    # Determines which co-ordinates on a 2D grid are visible
    # from a particular co-ordinate.
    # start_x, start_y: center of view
    # radius: how far field of view extends
    def do_fov(start_x, start_y, radius)
        light start_x, start_y
        8.times do |oct|
            cast_light start_x, start_y, 1, 1.0, 0.0, radius,
                @@mult[0][oct],@@mult[1][oct],
                @@mult[2][oct], @@mult[3][oct], 0
        end
    end

    private
    # Recursive light-casting function
    def cast_light(cx, cy, row, light_start, light_end, radius, xx, xy, yx, yy, id)
        return if light_start < light_end
        radius_sq = radius * radius
        (row..radius).each do |j| # .. is inclusive
            dx, dy = -j - 1, -j
            blocked = false
            while dx <= 0
                dx += 1
                # Translate the dx, dy co-ordinates into map co-ordinates
                mx, my = cx + dx * xx + dy * xy, cy + dx * yx + dy * yy
                # l_slope and r_slope store the slopes of the left and right
                # extremities of the square we're considering:
                l_slope, r_slope = (dx-0.5)/(dy+0.5), (dx+0.5)/(dy-0.5)
                if light_start < r_slope
                    next
                elsif light_end > l_slope
                    break
                else
                    # Our light beam is touching this square; light it
                    light(mx, my) if (dx*dx + dy*dy) < radius_sq
                    if blocked
                        # We've scanning a row of blocked squares
                        if blocked?(mx, my)
                            new_start = r_slope
                            next
                        else
                            blocked = false
                            light_start = new_start
                        end
                    else
                        if blocked?(mx, my) and j < radius
                            # This is a blocking square, start a child scan
                            blocked = true
                            cast_light(cx, cy, j+1, light_start, l_slope,
                                radius, xx, xy, yx, yy, id+1)
                            new_start = r_slope
                        end
                    end
                end
            end # while dx <= 0
            break if blocked
        end # (row..radius+1).each
    end
end
```
