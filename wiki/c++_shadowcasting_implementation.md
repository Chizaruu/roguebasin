# C++ shadowcasting implementation

---

A C++ implementation of Bjorn Bergstrom's [recursive shadowcasting FOV algorithm](fov_using_recursive_shadowcasting.md). It's mostly a port of the [Python version](python_shadowcasting_implementation.md). However it is not a working, compilable program, but rather just an example you can use as a starting point.

The code has unsigned int typedef'd to uint, and uses it where appropiate.

```cpp
#include <cmath>

typedef unsigned int uint;

static int multipliers[4][8] = {
    {1, 0, 0, -1, -1, 0, 0, 1},
    {0, 1, -1, 0, 0, -1, 1, 0},
    {0, 1, 1, 0, 0, -1, -1, 0},
    {1, 0, 0, 1, -1, 0, 0, -1}
};


class Map {
    public:
        Map() {};

        void set_visible(uint x, uint y, bool visible) {
            // Set the visibility of the cell at the given position.
        }

        uint get_width() const {
            // Return the width of the map.
            return 0;
        }

        uint get_height() const {
            // Return the height of the map.
            return 0;
        }

        bool is_opaque(uint x, uint y) const {
            // Return whether the given position holds an opaque cell.
            return false;
        }
};

void cast_light(Map& map, uint x, uint y, uint radius, uint row,
        float start_slope, float end_slope, uint xx, uint xy, uint yx,
        uint yy) {
    if (start_slope < end_slope) {
        return;
    }
    float next_start_slope = start_slope;
    for (uint i = row; i <= radius; i++) {
        bool blocked = false;
        for (int dx = -i, dy = -i; dx <= 0; dx++) {
            float l_slope = (dx - 0.5) / (dy + 0.5);
            float r_slope = (dx + 0.5) / (dy - 0.5);
            if (start_slope < r_slope) {
                continue;
            } else if (end_slope > l_slope) {
                break;
            }

            int sax = dx * xx + dy * xy;
            int say = dx * yx + dy * yy;
            if ((sax < 0 && (uint)std::abs(sax) > x) ||
                    (say < 0 && (uint)std::abs(say) > y)) {
                continue;
            }
            uint ax = x + sax;
            uint ay = y + say;
            if (ax >= map.get_width() || ay >= map.get_height()) {
                continue;
            }

            uint radius2 = radius * radius;
            if ((uint)(dx * dx + dy * dy) < radius2) {
                map.set_visible(ax, ay, true);
            }

            if (blocked) {
                if (map.is_opaque(ax, ay)) {
                    next_start_slope = r_slope;
                    continue;
                } else {
                    blocked = false;
                    start_slope = next_start_slope;
                }
            } else if (map.is_opaque(ax, ay)) {
                blocked = true;
                next_start_slope = r_slope;
                cast_light(map, x, y, radius, i + 1, start_slope, l_slope, xx,
                        xy, yx, yy);
            }
        }
        if (blocked) {
            break;
        }
    }
}

void do_fov(Map& map, uint x, uint y, uint radius) {
    for (uint i = 0; i < 8; i++) {
        cast_light(map, x, y, radius, 1, 1.0, 0.0, multipliers[0][i],
                multipliers[1][i], multipliers[2][i], multipliers[3][i]);
    }
}
```
