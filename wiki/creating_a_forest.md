# Creating A Forest

---

Ok first of all I would just like to say first this is pretty simple but it gets the job done. Second I stumbled across this by accident and it is very easy to do. I am using Borland Delphi 3. No wait don't panic if your using C++ first if you know nothing of programming I can translate it for you if you send me an e-mail message(address listed below). Second Borland Delphi 3 is VERY similar to C++ so if you know a bit about C++ then it should be no problem transelateing it. Anyway here we go...

First of all we'll need to assume a few things. First map is an array currently being used for the map, it is 50x50. Second that T is used for tree and G is used for ground. Ok lets begin.

```text
  procedure TForm1.DrawForest(x,y:integer); /* this is the void*/
  var            /* this allows us to declair our variables*/
  n,s,e,w,i,j,k,d: integer;
  begin              /* this states where the code for procedure DrawForest begins*/
    i := x;            /* this is so that it knows where to generate around or actually originally intended to be the start of a hall way*/
    j := y;
    for k := 1 to 20 do               /* this is how large it will be adjust it to adapt it to the size intended (note: 40 will not be double size*/
    begin     /*where the do begins*/    /*double size is around a hundred because it draws over itself)*/
      n := random(6); /* north south east west there at six to not make the forest too big and have a predictable pattern*/
      e := random(6);
      s := random(6);
      w := random(6);
      if n = 1 then        /* if we draw north then... if we dont, skip this*/
      begin     /* the if then statement begins here, if n did infact equal 1*/
        i := i - 1;            /* this is to ensure that when we restart at the end that we draw from the same location*/
        map[i,j] := 'T';   /* this makes map at location i , j tree*/
      end;                /* the if then statement ends here*/
      if s = 1 then    /* do the above but go south*/
      begin
        i := i + 1;
        map[i,j] := 'T';
      end;
      if e = 1 then    /*again but east*/
      begin
        j := j + 1;
        map[i,j] := 'T';
      end;
      if w = 1 then        /* and west*/
      begin
        j := j - 1;
        map[i,j] := 'T';
      end;
    end;     /* this is the end for the for do statement at the top, or rather it starts over here*/
  end;     /* this is the end for the entire procedure*/
```

Ok now we can draw a cavern like shape around x,y now next we need to pick a location randomly on our map to place the forest so we'll create a button for this task.

```text
 procedure TForm1.Button1Click(Sender: TObject);  /* this is the void for the button when its clicked*/
 var x,y,z,i: integer;  /* here we defin x and y our location to draw the forest on the map and z to decide how many times to do this*/
 begin    /* our procedure begins*/            /* and i to put in a for do statement */
   z := random(10);     /* this is randomizeing how many forests we'll have*/
   for i := 1 to z do      /* our for do statement to draw a forest in a random location z amount of times*/
   begin                      /* our for do statement begins*/
     x := random(50);      /* it randomly picks an x,y location on our map to draw a forest on*/
     y := random(50);
     drawforest(x,y);         /* this calls our procedure to make it draw a forest around x,y*/
   end;
 end;
```

and TA-DA you have an over world forest. There are many other things that you can do with this, my personal favorite is to use it to randomly draw cavern like rooms in my dungeon by change the T to G and to have it drawn at the end of a hall. There are many other uses for it but it would take to go into depth and explain it to all you C++ users. So that's all for now. For any questions or comments e-mail me at:

strait@mail2.quiknet.com
