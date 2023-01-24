# Digital field of view implementation

---

This is an explanation of how to implement the [Digital field of view](digital_field_of_view.md) algorithm.

Unlikely as it seems, there are no artifacts here. You can test the demo program at <http://reflexivelos.googlecode.com/svn/trunk/los2.cpp>. It contains an implementation of the not-so-dumb algorithm and the O(N<sup>2</sup>) algorithm.

## Dumb algorithm

---

The algorithm is simple: generate each digital line in turn, and walk along each one until you hit an obstruction. Here's a simple pseudocode for later reference:

```text
for 0 <= p <= q <= n and 0 <= s < q
  eps = s, y = 0
  for x = 1 to n
    eps = eps + p
    if (eps >= q)
      eps = eps - q
      y = y + 1

    mark_visible(x, y)

    if (obstruction(x, y))
      leave inner loop
```

Obviously, this is extremely slow - running time is O(N4).

## Not so dumb

---

Notice that, in the above pseudocode, if we fix p and q, and let s vary, there are only two possible heights for the line at any particular x coordinate. If both of those squares are blocked, no value of s will work, and if both are empty, they don't impose any extra conditions on s. If the bottom one is blocked, then smaller values of s will be excluded, and similarly for the top. So at each x coordinate, there will be an interval of possible values for s, which can be maintained easily.

This is basically a beam casting algorithm using each slope with denominator less than n.

This does a little better - running time is O(N<sup>3</sup>). But best possible is O(N<sup>2</sup>)...

## Between two points

---

Let's take a break from field of view algorithms... with a line of sight algorithm!

At this point, we need a little bit of geometric intuition. As far as digital lines in the first octant are concerned, all of the action happens at integral x coordinates (actually integral + 0.5, but we can shift our coordinates) - each obstruction can be thought of as a vertical line segment of length one, as can the source and destination. We want to know whether there is a line connecting the source line segment to the destination line segment that doesn't cross any of the obstructing segments. Thus, we can restrict attention to the parallelogram formed by the source and destination segments. Just like the previous algorithm, for each value of x there are only two relevant segments intersecting the parallelogram, each of which may or may not be obstructing.

A picture is worth a thousand words: dlosxz0.gif

Thus, obstructions can be classified as upper obstructions and lower obstructions, depending on which edge of the parallelogram they intersect. We want to know whether there is a line going underneath all of the upper obstructions and above all of the lower obstructions, that is, whether the convex hull of the upper obstructions (the blue area in the picture) intersects the convex hull of the lower obstructions (the green area). This is easy to check, though - we can find the convex hulls of each set in O(N) time, and then check this condition at every integral x coordinate from 1 to n.

## An idea inspired by Beam Casting

---

In the previous algorithm, we could have been a little bit more ambitious. Instead of simply checking if we can see the destination, why don't we check if we can see the intermediate segments (or at least, their intersections with the parallelogram we're restricting ourselves to)?

If we can do this, then we can solve the field of view problem in O(N<sup>2</sup>) time! We simply call the hypothetical algorithm with destinations (n, 0), (n, 1), ..., (n, n). This works because the parallelograms formed from the source and each destination completely cover the first octant, so if an intermediate segment is visible, a visible part of it will be contained in one particular parallelogram, and the segment will be marked visible while running the hypothetical algorithm with the corresponding destination:

dfov1ta8.gif

### So how do we make this hypothetical algorithm?

---

To check whether a lower segment is visible, given that we've calculated the convex hull of the obstructions we've seen so far, we can try pretending that the corresponding upper segment was an obstruction. If we then realize that the updated convex hulls would intersect, this tells us that our lower segment wasn't visible anyways. Unfortunately for this method, pretending something's an obstruction when it's not could make us use more than O(N) time in the end. We have to be trickier!

The trick is to also keep in memory the two unobstructed lines from the source segment with greatest and least slope. Let's go back to processing the lower segment. If the top point of the lower segment is below the line of least slope (which necessarily starts from the source segment, is tangent to the upper convex hull, then tangent to the lower convex hull), then pretending the upper segment is an obstruction implies that the updated convex hulls would intersect, so in this case the lower segment is invisible. If the top point of the lower segment is above the line of least slope, then the part of the lower segment just above that line is visible. So this tells us whether the lower segment is visible (one subtlety - we need to check that at least part of the visible region is still within the parallelogram, or we will come to grief).

updating the information: dfov2by9.gif

To update the lines of greatest and least slope, let's assume that the lower segment is an obstruction (in the picture, the upper segment is an obstruction). If it is invisible, it doesn't affect anything, so let's also assume it's visible. If the line of greatest slope changes here, then the convex hulls intersect, so we'll quit anyways. The line of least slope changes, though - the point of tangency to the lower convex hull moves to the top of the lower segment, and the point of tangency to the upper convex hull has to move to the right (since the endpoints of the lines of greatest and least slope always move to the right, we spend at most O(N) time updating them).

And that's all there is to it!

Note that if the visible area is entirely contained in an M\*M grid, and we use this method with a radius of N, the running time is O(NM). This is because for each of the N parallelograms, we take up to O(M) steps before we realize that we can't see anything in this parallelogram anymore.

## A Permissive Field of View algorithm

---

The algorithm just described can be very easily transformed into a [Permissive Field of View](permissive_field_of_view.md) algorithm! For simplicity, we restrict attention to the first quadrant. Then, as far as we are concerned, each square may as well be replaced by a (negative slope) diagonal line segment. Just as before, each parallelogram from the source segment to a destination segment intersects an upper and a lower segment along each intermediate (negative slope) diagonal line, and the parallelograms from the source to the destinations (0, n), (1,n), ..., (n, n), (n, n-1), ..., (n, 0) completely cover the first quadrant.

The modified version of Bresenham's algorithm, to make it give us the lower-right diagonal segments intersecting our parallelogram of slope p/q, looks like this:

```c++
eps = 0, x = 0, y = 0

while x + y <= 2*n
  eps = eps + p
  if (eps >= p + q)
    eps = eps - (p + q)
    y = y + 1
  else
    x = x + 1
```

For the upper-left ones, we use the same algorithm with eps started out at p+q-1. Also, we need to change things like "top point of lower segment" in the original algorithm to things like "top-left point of lower-right segment".

The last modification we need to make to our previous algorithm is allowing it to see between corners. While the previous algorithm would count the upper and lower convex hulls touching tangentially as obstructing our view, the new algorithm will count them touching tangentially as not obstructing the view, so a couple of >= signs will be switched to > signs, and vice versa. Once we do this, a new corner case appears, in which the top-left end of the source segment is on the line of greatest slope (or the bottom-right on the line of least slope), which we need to add an extra test for.

After making these modifications, the results agree with the [Precise Permissive Field of View](precise_permissive_field_of_view.md) algorithm. A demo implementation can be found at <http://reflexivelos.googlecode.com/svn/trunk/pfov.cpp>.
