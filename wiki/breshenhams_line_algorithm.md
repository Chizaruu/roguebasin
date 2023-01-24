# Bresenham's Line Algorithm

---

**Bresenham's Line Algorithm** is a way of drawing a line segment onto a square grid. It is especially useful for roguelikes due to their cellular nature. A detailed explanation of the algorithm can be found [here](http://www.cs.helsinki.fi/group/goa/mallinnus/lines/bresenh.html).

In [libtcod](libtcod.md) it is accessible using `line(x1, y1, x2, y2, callback)`. Below are several hand-coded implementations in various languages.

## C#

---

Here is a simple way of using the algorithm in C# with delegates.

```csharp
// Author: Jason Morley (Source: http://www.morleydev.co.uk/blog/2010/11/18/generic-bresenhams-line-algorithm-in-visual-basic-net/)
using System;

namespace Bresenhams
{
    /// <summary>
    /// The Bresenham algorithm collection
    /// </summary>
    public static class Algorithms
    {
        private static void Swap<T>(ref T lhs, ref T rhs) { T temp; temp = lhs; lhs = rhs; rhs = temp; }

        /// <summary>
        /// The plot function delegate
        /// </summary>
        /// <param name="x">The x co-ord being plotted</param>
        /// <param name="y">The y co-ord being plotted</param>
        /// <returns>True to continue, false to stop the algorithm</returns>
        public delegate bool PlotFunction(int x, int y);

        /// <summary>
        /// Plot the line from (x0, y0) to (x1, y10
        /// </summary>
        /// <param name="x0">The start x</param>
        /// <param name="y0">The start y</param>
        /// <param name="x1">The end x</param>
        /// <param name="y1">The end y</param>
        /// <param name="plot">The plotting function (if this returns false, the algorithm stops early)</param>
        public static void Line(int x0, int y0, int x1, int y1, PlotFunction plot)
        {
            bool steep = Math.Abs(y1 - y0) > Math.Abs(x1 - x0);
            if (steep) { Swap<int>(ref x0, ref y0); Swap<int>(ref x1, ref y1); }
            if (x0 > x1) { Swap<int>(ref x0, ref x1); Swap<int>(ref y0, ref y1); }
            int dX = (x1 - x0), dY = Math.Abs(y1 - y0), err = (dX / 2), ystep = (y0 < y1 ? 1 : -1), y = y0;

            for (int x = x0; x <= x1; ++x)
            {
                if (!(steep ? plot(y, x) : plot(x, y))) return;
                err = err - dY;
                if (err < 0) { y += ystep;  err += dX; }
            }
        }
    }
}
```

## C++

---

Here's a C++ version; plot() draws a "dot" at (x, y):

```c++
#include <cstdlib>

////////////////////////////////////////////////////////////////////////////////
void Bresenham(int x1,
    int y1,
    int const x2,
    int const y2)
{
    int delta_x(x2 - x1);
    // if x1 == x2, then it does not matter what we set here
    signed char const ix((delta_x > 0) - (delta_x < 0));
    delta_x = std::abs(delta_x) << 1;

    int delta_y(y2 - y1);
    // if y1 == y2, then it does not matter what we set here
    signed char const iy((delta_y > 0) - (delta_y < 0));
    delta_y = std::abs(delta_y) << 1;

    plot(x1, y1);

    if (delta_x >= delta_y)
    {
        // error may go below zero
        int error(delta_y - (delta_x >> 1));

        while (x1 != x2)
        {
            // reduce error, while taking into account the corner case of error == 0
            if ((error > 0) || (!error && (ix > 0)))
            {
                error -= delta_x;
                y1 += iy;
            }
            // else do nothing

            error += delta_y;
            x1 += ix;

            plot(x1, y1);
        }
    }
    else
    {
        // error may go below zero
        int error(delta_x - (delta_y >> 1));

        while (y1 != y2)
        {
            // reduce error, while taking into account the corner case of error == 0
            if ((error > 0) || (!error && (iy > 0)))
            {
                error -= delta_y;
                x1 += ix;
            }
            // else do nothing

            error += delta_x;
            y1 += iy;

            plot(x1, y1);
        }
    }
}
```

A template metaprogram implementation (requires the Boost.MPL library):

```c++
#include "boost/mpl/bool.hpp"
#include "boost/mpl/char.hpp"

#include "boost/mpl/for_each.hpp"

#include "boost/mpl/bitwise.hpp"
#include "boost/mpl/shift_left.hpp"

#include "boost/mpl/list.hpp"
#include "boost/mpl/push_front.hpp"

#include "boost/mpl/max.hpp"

#include "boost/mpl/minus.hpp"
#include "boost/mpl/arithmetic.hpp"

#include "boost/mpl/pair.hpp"

namespace mpl = boost::mpl;

template <std::size_t N,
  typename x1, typename y1,
  typename delta_x, typename delta_y,
  typename ix, typename iy,
  typename error, typename swap>
struct bresenham_line_pair :
  mpl::push_front<typename bresenham_line_pair<N - 1,
    typename mpl::plus<x1, ix>::type,
    typename mpl::if_c<(error::value > 0)
      || (!error::value && (ix::value > 0)),
      mpl::plus<y1, iy>, y1>::type,
    delta_x, delta_y,
    ix, iy,
    typename mpl::if_c<(error::value > 0)
      || (!error::value && (ix::value > 0)),
      mpl::minus<mpl::plus<error, delta_y>, delta_x>,
      mpl::plus<error, delta_y> >::type,
    swap>::type,
    typename mpl::if_<swap, mpl::pair<y1, x1>, mpl::pair<x1, y1> >::type
  >
{
};

template <typename x1, typename y1,
  typename delta_x, typename delta_y,
  typename ix, typename iy,
  typename error, typename swap>
struct bresenham_line_pair<0, x1, y1, delta_x, delta_y, ix, iy, error, swap> :
  mpl::list<typename mpl::if_<swap, mpl::pair<y1, x1>,
    mpl::pair<x1, y1> >::type>
{
};

template <int x1, int y1, int x2, int y2>
struct bresenham_line
{
  typedef typename mpl::minus<mpl::int_<x2>, mpl::int_<x1> >::type dx;
  typedef typename mpl::minus<mpl::int_<y2>, mpl::int_<y1> >::type dy;

  typedef typename mpl::if_<mpl::less<dx, mpl::int_<0> >,
    mpl::char_<-1>, mpl::char_<1> >::type ix;
  typedef typename mpl::if_<mpl::less<dy, mpl::int_<0> >,
    mpl::char_<-1>, mpl::char_<1> >::type iy;

  typedef typename mpl::max<dx, mpl::negate<dx> >::type abs_dx;
  typedef typename mpl::max<dy, mpl::negate<dy> >::type abs_dy;

  typedef typename mpl::shift_left<abs_dx, mpl::char_<1> >::type delta_x;
  typedef typename mpl::shift_left<abs_dy, mpl::char_<1> >::type delta_y;

  typedef typename mpl::if_<mpl::less<delta_x, delta_y>, mpl::bool_<true>,
    mpl::bool_<false> >::type swap;

  typedef typename mpl::if_<swap, mpl::minus<delta_x, abs_dy>,
    mpl::minus<delta_y, abs_dx> >::type error;

  typedef typename mpl::max<abs_dx, abs_dy>::type N;

  typedef typename mpl::if_<swap,
    bresenham_line_pair<N::value, mpl::int_<y1>, mpl::int_<x1>,
      delta_y, delta_x, iy, ix, error, swap>,
    bresenham_line_pair<N::value, mpl::int_<x1>, mpl::int_<y1>,
      delta_x, delta_y, ix, iy, error, swap> >::type::type sequence_type;
};

struct plotter
{
  template<typename T>
  inline void operator()(T)
  {
    plot(T::first::type::value, T::second::type::value);
  }
};

int main(int, char*[])
{
  mpl::for_each<bresenham_line<0, 0, 20, 10>::sequence_type>(plotter());

  return 0;
}
```

## Lua

---

Here's a Lua port:

```lua
function bresenham(x1, y1, x2, y2)
  delta_x = x2 - x1
  ix = delta_x > 0 and 1 or -1
  delta_x = 2 * math.abs(delta_x)

  delta_y = y2 - y1
  iy = delta_y > 0 and 1 or -1
  delta_y = 2 * math.abs(delta_y)

  plot(x1, y1)

  if delta_x >= delta_y then
    error = delta_y - delta_x / 2

    while x1 ~= x2 do
      if (error > 0) or ((error == 0) and (ix > 0)) then
        error = error - delta_x
        y1 = y1 + iy
      end

      error = error + delta_y
      x1 = x1 + ix

      plot(x1, y1)
    end
  else
    error = delta_x - delta_y / 2

    while y1 ~= y2 do
      if (error > 0) or ((error == 0) and (iy > 0)) then
        error = error - delta_y
        x1 = x1 + ix
      end

      error = error + delta_x
      y1 = y1 + iy

      plot(x1, y1)
    end
  end
end
```

## Python

---

This Python version returns a list of (x, y) tuples. It was converted from the Ruby version below, but also reverses the list to begin with the first coordinates.

```python
def get_line(start, end):
    """Bresenham's Line Algorithm
    Produces a list of tuples from start and end

    >>> points1 = get_line((0, 0), (3, 4))
    >>> points2 = get_line((3, 4), (0, 0))
    >>> assert(set(points1) == set(points2))
    >>> print points1
    [(0, 0), (1, 1), (1, 2), (2, 3), (3, 4)]
    >>> print points2
    [(3, 4), (2, 3), (1, 2), (1, 1), (0, 0)]
    """
    # Setup initial conditions
    x1, y1 = start
    x2, y2 = end
    dx = x2 - x1
    dy = y2 - y1

    # Determine how steep the line is
    is_steep = abs(dy) > abs(dx)

    # Rotate line
    if is_steep:
        x1, y1 = y1, x1
        x2, y2 = y2, x2

    # Swap start and end points if necessary and store swap state
    swapped = False
    if x1 > x2:
        x1, x2 = x2, x1
        y1, y2 = y2, y1
        swapped = True

    # Recalculate differentials
    dx = x2 - x1
    dy = y2 - y1

    # Calculate error
    error = int(dx / 2.0)
    ystep = 1 if y1 < y2 else -1

    # Iterate over bounding box generating points between start and end
    y = y1
    points = []
    for x in range(x1, x2 + 1):
        coord = (y, x) if is_steep else (x, y)
        points.append(coord)
        error -= abs(dy)
        if error < 0:
            y += ystep
            error += dx

    # Reverse the list if the coordinates were swapped
    if swapped:
        points.reverse()
    return points
```

## Ruby

---

Here's a Ruby version, it returns an array of points, each being a hash with 2 elements (x and y).

```ruby
def get_line(x0,x1,y0,y1)
  points = []
  steep = ((y1-y0).abs) > ((x1-x0).abs)
  if steep
    x0,y0 = y0,x0
    x1,y1 = y1,x1
  end
  if x0 > x1
    x0,x1 = x1,x0
    y0,y1 = y1,y0
  end
  deltax = x1-x0
  deltay = (y1-y0).abs
  error = (deltax / 2).to_i
  y = y0
  ystep = nil
  if y0 < y1
    ystep = 1
  else
    ystep = -1
  end
  for x in x0..x1
    if steep
      points << {:x => y, :y => x}
    else
      points << {:x => x, :y => y}
    end
    error -= deltay
    if error < 0
      y += ystep
      error += deltax
    end
  end
  return points
end
```

## VB.NET

---

Here is a generic way of using the algorithm in VB.NET using delegates.

```vbnet
' Author: Jason Morley (Source: http://www.morleydev.co.uk/blog/2010/11/18/generic-bresenhams-line-algorithm-in-visual-basic-net/)

Module BresenhamsLineAlgorithm
    Sub Swap(ByRef X As Long, ByRef Y As Long)
        Dim t As Long = X
        X = Y
        Y = t
    End Sub
    ' If the plot function returns true, the bresenham's line algorithm continues.
    ' if the plot function returns false, the algorithm stops
    Delegate Function PlotFunction(ByVal x As Long, ByVal y As Long) As Boolean
    Sub Bresenham(ByVal x1 As Long, ByVal y1 As Long, ByVal x2 As Long, ByVal y2 As Long, ByVal plot As PlotFunction)
        Dim steep As Boolean = (Math.Abs(y2 - y1) > Math.Abs(x2 - x1))
        If (steep) Then
            Swap(x1, y1)
            Swap(x2, y2)
        End If
        If (x1 > x2) Then
            Swap(x1, x2)
            Swap(y1, y2)
        End If
        Dim deltaX As Long = x2 - x1
        Dim deltaY As Long = Math.Abs(y2 - y1)
        Dim err As Long = deltaX / 2
        Dim ystep As Long
        Dim y As Long = y1
        If (y1 < y2) Then
            ystep = 1
        Else
            ystep = -1
       End If
       For x As Long = x1 To x2
            Dim result As Boolean
            If (steep) Then result = plot(y, x) Else result = plot(x, y)
            If (Not result) Then Exit Sub
            err = err - deltaY
            If (err < 0) Then
                y = y + ystep
                err = err + deltaX
            End If
       Next
    End Sub
    Function plot(ByVal x As Long, ByVal y As Long) As Boolean
        Console.WriteLine(x.ToString() + " " + y.ToString())
        Return True 'This just prints each co-ord
    End Function
    Sub Main()
        ' example
        Bresenham(1, 1, 10, 15, New PlotFunction(AddressOf plot))
        Console.ReadLine()
    End Sub
End Module
```

[Haskell](haskell.md)

A slightly verbose version in Haskell. See the discussion page for a variant one line shorter, but IMHO less readable. I bet other version, more readable and more succinct, can be written.

```haskell
-- | See <http://roguebasin.roguelikedevelopment.org/index.php/Digital_lines>.
balancedWord :: Int -> Int -> Int -> [Int]
balancedWord p q eps | eps + p < q = 0 : balancedWord p q (eps + p)
balancedWord p q eps               = 1 : balancedWord p q (eps + p - q)

-- | Bresenham's line algorithm.
-- Includes the first point and goes through the second to infinity.
bla :: (Int, Int) -> (Int, Int) -> [(Int, Int)]
bla (x0, y0) (x1, y1) =
  let (dx, dy) = (x1 - x0, y1 - y0)
      xyStep b (x, y) = (x + signum dx,     y + signum dy * b)
      yxStep b (x, y) = (x + signum dx * b, y + signum dy)
      (p, q, step) | abs dx > abs dy = (abs dy, abs dx, xyStep)
                   | otherwise       = (abs dx, abs dy, yxStep)
      walk w xy = xy : walk (tail w) (step (head w) xy)
  in  walk (balancedWord p q 0) (x0, y0)
```

[ActionScript](actionscript.md)

Here's an ActionScript implementation that creates a Line object with an array of points.

```actionscript
package
{
  import flash.geom.Point;

  public class Line
  {
     public var points:Array = [];

     public function Line(x0:int, y0:int, x1:int, y1:int) {
        calculate(x0, y0, x1, y1);
     }

     private function calculate(x0:int, y0:int, x1:int, y1:int):void {
      var dx:int = Math.abs(x1 - x0);
      var dy:int = Math.abs(y1 - y0);
      var sx:int = x0 < x1 ? 1 : -1;
      var sy:int = y0 < y1 ? 1 : -1;
      var err:int = dx - dy;

      while (true){
       points.push(new Point(x0, y0));

       if (x0==x1 && y0==y1)
          break;

       var e2:int = err * 2;
       if (e2 > -dx) {
          err -= dy;
          x0 += sx;
       }
       if (e2 < dx){
          err += dx;
          y0 += sy;
       }
      }
    }
  }
}
```

## Go

---

This is a translation for golang from the Python example above, it returns a slice to a struct that contains two ints.

```go
type Vector2i struct {
    X, Y int
}

func getLine(pos1, pos2 Vector2i) (points []Vector2i) {
    x1, y1 := pos1.X, pos1.Y
    x2, y2 := pos2.X, pos2.Y

    isSteep := abs(y2-y1) > abs(x2-x1)
    if isSteep {
       x1, y1 = y1, x1
       x2, y2 = y2, x2
    }

    reversed := false
    if x1 > x2 {
       x1, x2 = x2, x1
       y1, y2 = y2, y1
       reversed = true
    }

    deltaX := x2 - x1
    deltaY := abs(y2 - y1)
    err := deltaX / 2
    y := y1
    var ystep int

    if y1 < y2 {
    	ystep = 1
    } else {
    	ystep = -1
    }

    for x := x1; x < x2+1; x++ {
    	if isSteep {
    		points = append(points, Vector2i{y, x})
    	} else {
    		points = append(points, Vector2i{x, y})
    	}
    	err -= deltaY
    	if err < 0 {
    		y += ystep
    		err += deltaX
    	}
    }

    if reversed {
    	//Reverse the slice
    	for i, j := 0, len(points)-1; i < j; i, j = i+1, j-1 {
    		points[i], points[j] = points[j], points[i]
    	}
    }

    return

}

func abs(x int) int {
    switch {
    case x < 0:
        return -x
    case x == 0:
        return 0
    }
    return x
}
```

## JavaScript

---

Adapted from the C# example above, but improved so line is drawn in original direction. Demo here: [[1]](https://jsfiddle.net/vpkbunqt/10/).

```javascript
function drawline(x0,y0,x1,y1){
  var tmp;
  var steep = Math.abs(y1-y0) > Math.abs(x1-x0);
  if(steep){
    //swap x0,y0
    tmp=x0; x0=y0; y0=tmp;

    //swap x1,y1
    tmp=x1; x1=y1; y1=tmp;
  }

  var sign = 1;
  if(x0>x1){
    sign = -1;
    x0 _= -1;
    x1 _= -1;
  }
  var dx = x1-x0;
  var dy = Math.abs(y1-y0);
  var err = ((dx/2));
  var ystep = y0 < y1 ? 1:-1;
  var y = y0;

  for(var x=x0;x<=x1;x++){
    if(!(steep ? plot(y,sign*x) : plot(sign*x,y))) return;
    err = (err - dy);
    if(err < 0){
      y+=ystep;
      err+=dx;
    }
  }
}
```

## Rust

---

This is a translation for Rust from the Go example above, it returns a Vec of structs that contains two ints. (tested rustc ver 1.14.0)

```rust
#[derive(Copy, Clone, Debug)]
struct Point2d{
    pub x: u32,
    pub y: u32
}
fn get_line(a: Point2d, b: Point2d) -> Vec<Point2d> {
    let mut points = Vec::<Point2d>::new();
    let mut x1 = a.x as i32;
    let mut y1 = a.y as i32;
    let mut x2 = b.x as i32;
    let mut y2 = b.y as i32;
    let is_steep = (y2-y1).abs() > (x2-x1).abs();
    if is_steep {
        std::mem::swap(&mut x1, &mut y1);
        std::mem::swap(&mut x2, &mut y2);
    }
    let mut reversed = false;
    if x1 > x2 {
        std::mem::swap(&mut x1, &mut x2);
        std::mem::swap(&mut y1, &mut y2);
        reversed = true;
    }
    let dx = x2 - x1;
    let dy = (y2 - y1).abs();
    let mut err = dx / 2;
    let mut y = y1;
    let ystep: i32;
    if y1 < y2 {
        ystep = 1;
    } else {
        ystep = -1;
    }
    for x in x1..(x2+1) {
        if is_steep {
            points.push(Point2d{x:y as u32, y:x as u32});
        } else {
            points.push(Point2d{x:x as u32, y:y as u32});
        }
        err -= dy;
        if err < 0 {
            y += ystep;
            err += dx;
        }
    }

    if reversed {
        for i in 0..(points.len()/2) {
            let end = points.len()-1;
            points.swap(i, end-i);
        }
    }
    points
}
```
