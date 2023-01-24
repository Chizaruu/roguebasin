# Permissive-fov

---

<table>
    <tr style="text-align: center">
        <td colspan="2" text-align="center">Permissive-fov</td>
    </tr>
    <tr style="text-align: center">
        <td colspan="2">Library project</td>
    </tr>
    <tr>
        <td style="text-align: center">Developer</td>
        <td>Jonathon Duerig</td>
    </tr>
    <tr>
        <td style="text-align: center">Released</td>
        <td>2007-04-23</td>
    </tr>
    <tr>
        <td style="text-align: center">Updated</td>
        <td></td>
    </tr>
    <tr>
        <td style="text-align: center">Status</td>
        <td>Complete</td>
    </tr>
    <tr>
        <td style="text-align: center">Licensing</td>
        <td>BSD License</td>
    </tr>
    <tr>
        <td style="text-align: center">P. Language</td>
        <td>C, C++</td>
    </tr>
    <tr>
        <td style="text-align: center">Platforms</td>
        <td>Any</td>
    </tr>
    <tr>
        <td style="text-align: center">Dependencies</td>
        <td>None</td>
    </tr>
    <tr>
        <td colspan="2" style="text-align: center">[Official site of Permissive-fov]</td>
    </tr>
</table>

A library for [Permissive Field of View](permissive_field_of_view.md).

This is a loose adaptation of [libfov](libfov.md) to the problem of [Permissive Field of View](permissive_field_of_view.md). It can be found as either a [tarball](http://jonathonduerig.com/permissive-fov/permissive-fov-1.0.tar.gz) or a [zip file](http://jonathonduerig.com/permissive-fov/permissive-fov-1.0.zip). The usage and API are described there.

A more complete description of the algorithm used in permissive-fov can be found at [Precise Permissive Field of View](precise_permissive_field_of_view.md).

The following is reproduced from the overview of the README file of the library:

## Overview

---

This library provides an implementation of precise permissive field of view calculations on coarse-grained maps. A map is a grid where squares are either completely open or completely obstructed.

Permissive field of view means that a destination square is visible from a source square if and only if there exists an unobstructed line from some point in the source square to some point in the destination square. This is equivalent to finding the shadows cast by an area (as opposed to point) light source.

Precise field of view means that the algorithm does not make any approximations, nor does it make use of floating point numbers. This means that the algorithm (assuming it is bug-free) will produce neither inaccuracies nor anomalies. It also means that there will be no possibility of a rounding error.

The algorithm has a complexity of approximately O(n^2) where n is the radius. I say approximately, because it is difficult to come up with a worst case scenario. Generally, it will tend to perform worse on open areas and better on cramped areas. On a completely open area, it performs at exactly O(n^2).
