# Grid Based Dungeon Generator

---

## Introduction

---

The grid-based dungeon generator is an easy method to generate maps that contain rooms guaranteed to fit within the dungeon space. By dividing the dungeon space into a set of cells, rooms can be placed into the space without the need to check whether each room overlaps another room, or falls outside of the dungeon space. This article describes how to use the grid-based generator to create the basic dungeon layout.

In most roguelike games, the dungeon is an array of some data type where each cell within the array defines a tile (a location) within the dungeon. The data type may be a simple data type such as an integer, or a more complicated composite type such as a structure. The terrain features of the dungeon, that is the walls, floors, doors and so on, usually are only a part of the overall dungeon data description. For purposes of illustration, a small array of an undefined data type will be used to show the principles of the technique. In practice the grid would be much larger and the array would be of a specific data type.

## Implementation

---

### Some Terminology

A dungeon starts with an array of some data type. Visually the array may look like Figure 1.

```text
            1         2         3
  0123456789012345678901234567890123456789
 0
 1
 2
 3
 4
 5
 6
 7
 8
 9
10
 1
 2
 3
 4
 5
 6
 7
 8
 9
```

**Figure 1: An empty array.**

In Figure 2 the asterisk is located at the index [0, 0] where the first 0 is the row, and the second 0 is the column.

```text
            1         2         3
  0123456789012345678901234567890123456789
 0*
 1
 2
 3
 4
 5
 6
 7
 8
 9
10
 1
 2
 3
 4
 5
 6
 7
 8
 9
```

**Figure 2: Index [0, 0] contains an asterisk.**

While these diagrams are crude, they will be used to visually explain the process.

### Creating the Grid

The first step in the process (after creating an array to hold the data values) is to create a grid that will be used to create the rooms of the dungeon. The grid is composed of logical units called cells. The cells define the maximum extent of a room, as well as its position on the map. Since the example array is quite small, the cells will be defined with a size of 5. Once the size has been chosen, the array is divided into a set of cells as seen in Figure 3.

```text
            1         2         3
  0123456789012345678901234567890123456789
 0    |    |    |    |    |    |    |    |
 1 1  | 2  |    |    |    |    |    |    |
 2    |    |    |    |    |    |    |    |
 3    |    |    |    |    |    |    |    |
 4=========================================
 5    |    |    |    |    |    |    |    |
 6    |    |    |    |    |    |    |    |
 7    |    |    |    |    |    |    |    |
 8    |    |    |    |    |    |    |    |
 9=========================================
10    |    |    |    |    |    |    |    |
 1    |    |    |    |    |    |    |    |
 2    |    |    |    |    |    |    |    |
 3    |    |    |    |    |    |    |    |
 4=========================================
 5    |    |    |    |    |    |    |    |
 6    |    |    |    |    |    |    |    |
 7    |    |    |    |    |    |    |    |
 8    |    |    |    |    |    |    |    |
 9    |    |    |    |    |    |    |    |
```

**Figure 3: Array divided into a grid of cells.**

In figure 3, cell 1 has the dimensions (row first) [0, 0] [4, 0], [0, 4], [4, 4]. Cell 2 has the dimensions [0, 5] [4, 5], [0, 9], [4, 9]. Each cell is a 5 by 5 square. In order to create the cell structure, you would iterate over the array, dividing the array by the size of the cell (5 in this case) and storing the dimensions of each cell into a temporary data structure.

To calculate the array size take the size of the array and divide by the cell size. The example array is 20 x 40 units, so 20 / 5 = 4 and 40 / 5 = 8, so the structure array would contain 32 elements. The temporary data structure array would contain the dimensions of each cell on the map.

Once the grid is in place, the map can be populated with rooms. Since the cell size is 5, a room could be a maximum of size of 4, to a desired minimum size. Typically, the number of rooms would be randomly generated as well as the width and height of each room. Once the room list has been generated, a cell in the grid is randomly selected and the room is drawn inside the cell. Figure 4 illustrates the grid with some rooms added.

```text
            1         2         3
  0123456789012345678901234567890123456789
 0****|    | ***|    |    |    |    |    |
 1*  *|    | * *|    |    |    |    |    |
 2*  *|    | * *|    |    |    |    |    |
 3****|    | ***|    |    |    |    |    |
 4=========================================
 5    |    |****|    |    |*** |    |    |
 6    |    |*  *|    |    |* * |    |    |
 7    |    |*  *|    |    |*** |    |    |
 8    |    |****|    |    |    |    |    |
 9=========================================
10    |    |    |*** |    |****|    |    |
 1    |    |    |* * |    |*  *|    |    |
 2    |    |    |* * |    |*  *|    |    |
 3    |    |    |*** |    |****|    |    |
 4=========================================
 5****|    |    |    |****|    |    |    |
 6*  *|    |    |    |*  *|    |    |    |
 7****|    |    |    |*  *|    |    |    |
 8    |    |    |    |****|    |    |    |
 9    |    |    |    |    |    |    |    |
```

**Figure 4: Array with rooms added.**

It is easy to see that each room will automatically fit within the map array, and no room will overlap another room. This reduces quite a bit of code that is normally needed to ensure a room will fit on the map, will not overlay another room and stays within the array boundaries. As rooms are added to the map, each cell is marked as used, so that the room adding code will know that a cell is occupied and will look for an empty cell.

### Connecting the Rooms

Once the rooms are created they can be connected in a variety of ways to produce different styles of dungeons. For example, a flood-fill corridor connection algorithm would create a dungeon where each room would "fan" from a central seed room, requiring a lot of backtracking to visit all the rooms. A "connect this room to last room" algorithm would create a lot of corridors and rooms that have multiple doorways. The dungeon generator could use a number of corridor building algorithms to create a wide range of dungeon styles.

### Non-standard Rooms

Since a room is constrained by the cell it occupies, it is quite easy to create rooms that are not square without worrying about whether the room will fit on the map and with the other rooms. The room drawing code could draw a circle or a triangle within the cell dimensions and the room will automatically fit on the map.

### Generating Doors

Creating doors for rooms is easier since the room list contains the room dimensions within the cell. As the corridor building algorithm reach a room, it simply checks to see if it has reached the border of the room. If it has, a door can be placed.

### Themed Dungeons

Since the grid has a regular structure, creating themed dungeons is much easier since the theme can be based on a regular structure rather than a random structure. Themes usually have some order to them, some sequence inherent in the theme, and having a grid layout to help guide the theme reduces the code necessary to build the theme.

## Summary

---

The grid-based dungeon generator is a fast and simple way to create dungeons. The amount of error checking code that is normally associated with dungeon building is not needed since the rooms of the dungeon are constrained by the cell structure of the grid. This makes the dungeon building code smaller, faster and easier to maintain.

There is as much freedom when using the grid as there is with a free-form layout, without the complication. The grid can be densely packed with rooms to create a high density dungeon, or sparsely packed, to create a lot of corridors and widely connected rooms. The cell constraints makes creating non-standard rooms much easier.

Since the corridor building code is separate from the room building code, many types of dungeons can be created using different corridor building algorithms to create virtually any type of dungeon desired.

## Games That Use Grid-based Dungeon Generator

- [Escape From Lab 42](escape_from_lab_42.md) uses the grid-based dungeon generator to build all its maps.
- [QHack](qhack.md) uses a similar algorithm as described by the author.
