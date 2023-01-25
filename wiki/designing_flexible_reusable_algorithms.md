# Designing Flexible, Reusable Algorithms

---

Originally written by eclectocrat, aka Jeremy Jurksztowicz

There are a lot of algorithms for generating roguelike levels. The vast majority involve dealing with discrete 2 dimensional lattices, or 2D arrays of map values. What follows is one way of organizing your map generation code. I will be using C++ to demonstrate, but just about any language will do fine.

GLOSSARY:

- Cell - A value located at a discrete point in a map. A cell can represent a tile, an entity, or anything which is located in space.
- Map - A 2D array of cell values.
- Cell Predicate - A function which which accepts a location in a map and returns whether or not the given location matches the predicates criteria.
- Cell Brush - A function which modifies one or more cells on a map.

Every level starts with a map. The map will start out with all it's cells set to some value indicating that it's 'empty'. I prefer to use integers for the cell values because they're fast, but you might want to use a structure that contains more information, whatever works for you. I'm going to be working with some imaginary classes, the functions should be self explanatory.

```cpp
Map level;
level.fill(0);
```

Here we are with a clear level. Next, I'd probably want to generate some content. Lets do that with an assumed generation algorithm 'GenerateCaves', like so:

```cpp
const int OPEN = 1;
const int WALL = 2;
GenerateCaves(level, OPEN, WALL);
```

This imaginary function takes two cell values, a cell representing open walkable space, and another representing a wall. The algorithm itself functions using one of the wonderful, magical methods detailed on this WIKI. Now that we have a basic map, we will probably want to do things with it. But how? We can manually scan each tile and decide what we might want to put there, or how we might modify it, but hardcoding such things makes the algorithms very brittle. This is where predicates and brushes make their entrance. Rather than dealing on actual cell values, algorithms will deal with abstracts, and become much more general. For example, assume we have a level made up of forests, caves and plains:

```cpp
Map output;

// Find all connected regions that are caves.
output.fill(0);
FloodFill(level, output, x, y, CellEquals(CAVE), DrawCell(OPEN))

// Find all connected regions that aren't caves.
output.fill(0);
vector<int> cells = {FOREST, PLAINS}
FloodFill(level, output, x, y, CellEqualsAny(cells.begin(), cells.end()), DrawCell(OPEN));
```

By implementing your own predicates and brushes you can customize the way algorithms behave. For instance, how about if you wanted to find an area that a particular monster might live in, but that monster is terrified of water, it'll never be anywhere close to a river or lake. All you need to do is implement a CellNotNearCell predicate and call your nice and flexible floodfill.

```cpp
// In this case we only want cells that are at least 2 spaces away from a cell containing water.
FloodFill(level, output, x, y, CellNotNearCell(WATER, 2), DrawCell(OPEN));
```

So how do we implement these magical, flexible algorithms? Here's one way to implement floodfill in C++, taken from my own project:

```cpp
template <typename CellPredicate, typename CellBrush>
void FloodFill(
    Map const *imap,
    Map *omap,
    CellBrush fill,
    CellPredicate impassable,
    Point p)
{
 assert(imap->size() == omap->size());
 assert(!impassable(imap, p));

 std::deque<Point> queue;
 queue.push_back(p);

 const Map::Cell temp = Map::tempCell();
 const Size mapsize = imap->size();
 while (!queue.empty())
 {
  p = queue.front();
  queue.pop_front();

  if (!impassable(imap, p) && omap->get(p) != temp)
  {
   omap->get(p) = temp;

   if (p.x > 0)
    queue.push_back(Point(p.x - 1, p.y));
   if (p.x < mapsize.w - 1)
    queue.push_back(Point(p.x + 1, p.y));
   if (p.y > 0)
    queue.push_back(Point(p.x, p.y - 1));
   if (p.y < mapsize.h - 1)
    queue.push_back(Point(p.x, p.y + 1));
  }
 }
 // Goes over the whole map replacing a cell satisfying the predicate with the brush contents.
 omap->replace(CellEquals(temp), fill);
}
```

In the case of my algorithms, the input and output map may or may not be the same. Regardless, I can tune this puppy to do any number of neat things by just inputing a different predicate or brush.

Here's the same code written in Lua, just for fun:

```lua
function FloodFill (imap, omap, fill, impassable, p)
  assert(imap:size() == omap:size())
  assert(type(impassable) == "function")
  assert(type(fill) == "function")
  assert(not impassable(imap, p))

  local queue = {p}
  local temp = Map.tempCell()
  local mapsize = imap:size()

  while #queue > 0 do
    p = queue[1]
    table.remove(queue, 1)

    if not impassable(imap, p) and omap:get(p) ~= temp then
      omap:set(p, temp)

      if p.x > 1 then
        table.insert(queue, {x=p.x-1, y=p.y})
      end
      if p.x < mapsize.w then
        table.insert(queue, {x=p.x+1, y=p.y})
      end
      if p.y > 1 then
        table.insert(queue, {x=p.x, y=p.y-1})
      end
      if p.y < mapsize.h then
        table.insert(queue, {x=p.x, y=p.y+1})
      end
    end
  end
  omap:replace(CellEquals(temp), fill)
end
```
