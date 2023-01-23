# Finite state name generator

---

[source](http://groups.google.com/group/rec.games.roguelike.development/msg/185c90b7e8986d30) : Original author [Jeff Lait](jeff_lait.md)

This generator uses a very simple finite state machine to select between individual letters in a fashion that creates pronounceable player names.

The comments at the top explain the nature of the beast. The only syllable based component is where I trigger the termination timer using a quickly accelerating random chance to keep names short.

Following code is in [C++](c++.md)

```c++
#include <stdio.h>
#include <stdlib.h>
#include <ctype.h>
#include <string.h>
#include <time.h>

//this function returns true if a random roll (1-100) is lower then c.
//So: rand_chance(20) has a 20% chance of returning true.
bool rand_chance(int c) {
   return( (rand() % 100 + 1) <= c);
}

//this function returns a random int between 0 and i-1.
int rand_choice(int i) {
   return rand() % i;
}

void rand_name(char *text, int len)
{
    // Very simple markov generator.
    // We repeat letters to make them more likely.
    const char *vowels = "aaaeeeiiiooouuyy'";
    const char *frictive = "rsfhvnmz";
    const char *plosive = "tpdgkbc";
    const char *weird = "qwjx";
    // State transitions..
    // v -> f, p, w, v'
    // v' -> f, p, w
    // f -> p', v
    // p -> v, f'
    // w, p', f' -> v

    int         syllables = 0;
    char        state;
    int         pos = 0;
    bool        prime = false;

    // Initial state choice
    if (rand_chance(30))
        state = 'v';
    else if (rand_chance(40))
        state = 'f';
    else if (rand_chance(70))
        state = 'p';
    else
        state = 'w';

    while (pos < len-1)
    {
        // Apply current state
        switch (state)
        {
            case 'v':
                text[pos++] = vowels[rand_choice(strlen(vowels))];
                if (!prime)
                    syllables++;
                break;
            case 'f':
                text[pos++] = frictive[rand_choice(strlen(frictive))];
                break;
            case 'p':
                text[pos++] = plosive[rand_choice(strlen(plosive))];
                break;
            case 'w':
                text[pos++] = weird[rand_choice(strlen(weird))];
                break;
        }

        // Chance to stop..
        if (syllables && pos >= 3)
        {
            if (rand_chance(20+pos*4))
                break;
        }

        // Transition...
        switch (state)
        {
            case 'v':
                if (!prime && rand_chance(10))
                {
                    state = 'v';
                    prime = true;
                    break;
                }
                else if (rand_chance(40))
                    state = 'f';
                else if (rand_chance(70))
                    state = 'p';
                else
                    state = 'w';
                prime = false;
                break;
            case 'f':
                if (!prime && rand_chance(50))
                {
                    prime = true;
                    state = 'p';
                    break;
                }
                state = 'v';
                prime = false;
                break;
            case 'p':
                if (!prime && rand_chance(10))
                {
                    prime = true;
                    state = 'f';
                    break;
                }
                state = 'v';
                prime = false;
                break;
            case 'w':
                state = 'v';
                prime = false;
                break;
        }
    }
    text[0] = toupper(text[0]);
    text[pos++] = '\0';

}

//Arguments:    First is max length of random name
//              Second is numer of random names
int main(int argc, char *argv[]) {
    srand ( time(NULL) );
    int j = 1;
    if(argc > 2) {
     j = atoi(argv[2]);
    }

    while(j > 0) {
         if(argc > 1) {
          int i = atoi(argv[1]);
          char * a = (char*) malloc(sizeof(char) * i);
          rand_name(a, i);
          printf("%s\n",a);
         } else {
          char a [5];
          rand_name(a, 5);
          printf("%s\n",a);
         }
         j--;
    }
    return 0;
}
```

(The code is public domain)

This program accepts the following arguments, the first is the max length of the name, but most will be smaller. And the second is the number of names to generate. Defaults are 5 1.

Results:

```text
./randomName 10 20
'tosezivc
Fdo
'dybze
Wask
Bof
Yqa
'vymi
Fatyope
Xar
Qow
Midu
Tezk
Tucik
Hgy
Eqyhp
Hti
Cevidi
Sd'gaquz
Ubeqy
Spa
```

As you can see the algorithm has a bias for short names, and is not always pronounceable. But it is a start. Also as this is a proof of concept implementation, the [random number generator](random_number_generator.md) is not properly seeded. Running multiple types after each other could result in the same random names (depending on the operating system).
