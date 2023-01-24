# Eligloscode

---

I wrote this as a pseudocode example of easy line of sight on the Rogue Temple. The first example casts a ray between the player and every square on the map within the view radius. The second example sends out rays in a 360 degree circle around the player. The second example should be faster, and can be largely precomputed.

```c
void FOV()
{
  int i,j;
  float x,y,l;
  for(i=0;i<80;i++)for(j=0;j<25;j++)
  {
    MAP[i][j]= NOT_VISIBLE;//Pseudo code
    x=i-PLAYERX;
    y=j-PLAYERY;
    l=sqrt((x*x)+(y*y));
    if(l<VIEW_RADIUS)
      if(DoFov(i,j)==true)
        MAP[i][j] = VISIBLE;//Pseudo code, you understand.
  };
};

bool DoFov(int x,int y)
{
  float vx,vy,ox,oy,l;
  int i;
  vx = x-PLAYERX;
  vy = y-PLAYERY;
  ox = (float)x+0.5f;
  oy= (float)y+0.5f;
  l=sqrt((vx*vx)+(vy*vy));
  vx/=l;
  vy/=l;
  for(i=0;i<(int)l;i++)
    {
    if(MAP[(int)ox][(int)oy]==BLOCK)
      return false;
    ox+=vx;
    oy+=vy;
    };
  return true;
};
```

If this is a bit slow, try only casting rays in a 360 degree or less circle around the player. You can convert degrees to radians and then use sin and cos to figure out the unit vector to use, which can be precomputed. Then you can cast rays along each of the degrees marking squares as visible until you hit a block at which point the ray terminates. This is much closer to raycasting than the above example.

Raycasting:

```c
void FOV()
{
  float x,y;
  int i;
  CLEAR_MAP_TO_NOT_VISIBLE();//Initially set all tiles to not visible.
  for(i=0;i<360;i++)
  {
    x=cos((float)i*0.01745f);
    y=sin((float)i*0.01745f);
    DoFov(x,y);
  };
};

void DoFov(float x,float y)
{
  int i;
  float ox,oy;
  ox = (float)PLAYERX+0.5f;
  oy = (float)PLAYERY+0.5f;
  for(i=0;i<VIEW_RADIUS;i++)
  {
    MAP[(int)ox][(int)oy]=VISIBLE;//Set the tile to visible.
    if(MAP[(int)ox][(int)oy]==BLOCK)
      return;
    ox+=x;
    oy+=y;
  };
};
```

You don't have to send out 360 rays, either. You just need to reach the number 360 in i. So you can exchange i++ for i+=2 or i+=4 or even i+=16. This will just result in "lower accuracy".

By [elig](elig.md)
