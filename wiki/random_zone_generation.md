# Random Zone Generation

---

In most roguelike games, the map is isotropic. That is, the map is mostly generated the same over it's entire area. Some games, such as Brogue, will post-process a generated map to add in a greater variety of features - such as bridges, chasms, and lakes - or a greater variety of terrain - rooms themed with swamp, forest, or quartz. However, the underlying map area generation is mostly the same. Some other games will implement a few different generation algorithms, and use a different one each time a new map is created. This is seen in some newer roguelike games such as Torchlight or Path of Exile, where certain maps need certain themes.

However, if you want to generate vastly different regions on the same map, a new technique is required. In this page, I outline a method which can divide a square map into several random zones - contiguous areas onto which different generators or themes can be applied. I will be using pseudo-C# to write code examples, since I am currently using that language often.

## Step 1: Perlin Noise Generation

---

The first step is to generate some noise which can be used to create the shapes of the zones. This is most easily accomplished using Perlin Noise (or Simplex Noise). Most Perlin noise generators have methods which produce a black-and-white noise texture. These textures alone are not enough to provide the necessary noise. However, if three different noise textures are combined into a single texture, one on each channel, we then have a good sample to work from.

The following code generates a texture synthesized from three different noise textures.

```csharp
Texture GenerateNoiseTexture(int seed) {
    Perlin noise = new Perlin();
    Color[][] noiseCol = new Color[3][];
    Color[] finalCol;
    float offsetx, offsety;

    for (int i = 0; i < 3; i++) {
        offsetx = Random.Range(-MAX_OFFSET, MAX_OFFSET);
        offsety = Random.Range(-MAX_OFFSET, MAX_OFFSET);

        noise.Generate(offsetx, offsety);
        noiseCol[i] = noise.ToTexture().GetPixels();
    }

    finalCol = new Color[noiseCol[0].Length];
    for (int i = 0; i < noiseCol[0].Length; i++) {
        finalCol[i] = new Color(noiseCol[0][i].greyscale, noiseCol[1][i].greyscale, noiseCol[2][i].greyscale);
    }

    return new Texture(finalCol);
}
```

## Step 2: Snap Colors to Specific Values

---

The next step is mostly to make the image easier to visually process. Right now, the noise texture returned by the previous function is filled with a mishmash of red, green, blue, white, and black, along with every color in between. To make this into the shape of the zones, we need to snap each pixel to a limited set of values. The easiest way to do this is to convert the color values to vector values. By calculating the distance between the the pixel-vector and some set of color-vectors, and taking the minimum, we can determine which is the closest color match for the current pixel.

The code for this is a little hard to write on here, since it involves many convenience functions (converting between color and vector, distances between vectors, a set of vectors to match to), but the idea here is a simple closest-neighbor approach. Loop through each pixel value in the texture, find the closest match in your set of colors, and set that pixel to that color. This works best when your set of colors is as wide as possible, and does not include white or black. Also note that the more colors in your set, the smaller each zone will be. I recommend a set of 6 colors, including red, blue, green, yellow, cyan, and magenta.

## Step 3: Find Zones

---

This is the key part in our algorithm, and it happens to be pretty simple: flood fill. If you are familiar with flood fill algorithms, or even just Dijkstra pathfinding, you will know how this is performed. I'll use an optimized version of Dijkstra to do this.

We also need to define a class to hold the information about our zone. The zone class has 3 necessary variables: minX, minY, and map. minX and minY store the upper left corner of the bounding box surrounding the zone. map stores a texture (or 2d byte array) which holds the outline of the zone (as well as the bounding box size, by inference).

The function moves through our noise texture pixel by pixel. If it detects a pixel which is NOT black, it starts a flood fill routine. The flood fill notes the color of the current pixel, and starts moving outward to find all pixels of the same color. As it goes, it sets visited pixels to black. This is part of the optimization that makes the flood fill run very quickly, the code implicitly knows which pixels have been visited. Note that a closed set is still needed, which holds the position of every pixel visited by the algorithm (semi-optimized with a new class - Coords).

NOTE: The optimization requires the destruction of the noise texture. It is highly recommended you destroy a copy of the noise texture, rather than the original. The original will be used in the next step.

```csharp
List<Zone> ExtractZones(Texture noise) {
    List<Zone> zones = new List<Zone>();

    for (int i = 0; i < noise.width; i++) {
        for (int j = 0; j < noise.height; j++) {
            if (noise.GetPixel(i,j) != Color.black) {
                zones.Add(FloodFill(noise, i, j));
            }
        }
    }
}

Zone FloodFill(Texture noise, int startx, int starty) {
    Color startCol = noise.GetPixel(startx, starty);
    List<Coord> openSet = new List<Coord>();
    openSet.Add(new Coord(startx, starty));
    int minX, minY, maxX, maxY;

    while (openSet.Count > 0) {
        if (noise.GetPixel(openSet[0].x+1, openSet[0].y) == startCol) {
            openSet.Add(new Coord(openSet[0].x+1, openSet[0].y));
        }
        //repeat for other pixels around chosen point
        closedSet.Add(openSet[0]);
        openSet.RemoveAtIndex(0);

        //do a check here to see if the processed pixel was at a minimum in x or y, or a maximum. If it was, set the new values.
    }

    Zone newzone = new Zone();
    newzone.minX = minX; newzone.minY = minY;
    newzone.map = new Texture(maxX-minX, maxY-minY);
    for (int i = 0; i < closedSet.Count; i++) {
        newzone.map.SetPixel(closedSet[i].x, closedSet[i].y, Color.white);
    }

    return newzone;
}
```

## Step 4: Reduce Noise

---

It sounds weird to say that we are reducing noise, but it's an essential step in this process. Right now, some of the zones are inevitably too small. Most of these will be 1 pixel in size. This is an inevitability due to the noise. Fortunately, it's relatively easy to get rid of these zones. There are two methods, which can be used individually, but work best together. The first is gaussian reduction, the second is surrounding absorption.

Gaussian reduction is kind of what it sounds like - we use gaussian blurring to cause these small zones to bleed into the surrounding zones. We don't really want to mess with the larger zones, so we want a way to mask them out. To do this, loop though all the zones on the map, and make a list of the smallest ones (below a certain size, or even just the smallest n zones). Use these zones to create a mask by combining all of their map information into a single level-sized texture. Loop though each pixel on the map (the larger one), and compare it to the mask. If the mask is black at that point, it is part of a larger region. If it is white, it is part of a smaller region, and we should apply a gaussian blur to that pixel.

Once the blur has been applied, repeat steps 2 and 3, then figure out if there are still small zones. It will probably take 3-4 iterations before you reach a point where the gaussian blur reaches equilibrium and no longer does anything. I also recommend starting with a larger radius blur and reducing it for each iteration, as this seems to help it reach equilibrium faster. There will probably still be small zones, but with any luck they will be vastly reduced and easier to deal with.

Surrounding Absorption is a much simpler process. Just take a small zone, find another zone next to it, and set both zones to the same color. After a repetition of steps 2 and 3, the two zones will become one, just like that. Care must be taken when determining how to choose the adjacent zone, as you can easily get stuck in an infinite loop where two small zones keep trying to absorb each other. However, if we use the same mask technique as the gaussian reduction, we can reduce this possibility drastically. This method also requires far fewer iterations to reach equilibrium, and will most likely be finished after just 2 repetitions.

## Step 5: Up to You

---

The algorithm is finished, and you now have a map area with several different defined zones. You can do whatever you like with this information, from creating an overworld where each zone represents a terrain feature, or a dungeon where each zone represents a generation type. You can have one zone use room generation, an adjacent one use cave generation, and connect them with some simple A\* tunnels. It's all up to you from this point on.

## Criticism, Concerns, etc

---

I am aware that there are some other ways to achieve this type of generation. Voronoi diagrams, for example, can create a series of polygons, and some simple midpoint displacement can add more randomness. However, my method guarantees more control over the size of each zone, and grants more noise to each zone shape with less work overall. It's up to you to decide if this method is better or worse, but I can say that with the right implementation, this method can generate zones on a 1000x1000 tile map within about a second.
