# Info File Variant - Compile-to-Code

---

by Henri Hakl

Using a variation of [Info Files](info_files.md) for flexibility of content while requiring no extensive scripting or programming.

## Introduction

---

In the past two weeks I've had time to read through and follow a large variety of articles on roguelike development. This served as a starting point to my own forays into roguelike programming, some of which have in turn been reflected in articles I authored.

This text outlines the most recent developments on my project - a variant on info files. James Burton's article on [Info_Files](info_files.md) is quite comprehensive and covers the use of hard code or scripting languages to implement info files. The purpose of info files is to emigrate game data into easy-to-manage text files; which allows easy porting or extension of races, monsters, skills, etc.

The key novelty this text introduces is to neither code an extensive engine to handle info files, nor to outsource the handling to existing scripting systems such as LUA: Instead the text introduces the idea of creating a code generator. The idea is quite simple - I wanted a powerful info file system that allowed complex expressions and conditions. But I only have access to [Turbo Pascal](pascal.md) on this machine, making elaborate third-party scripting systems inavailable; furthermore I didn't actually want to spend an excessive amount of time coding a suitably rich scanner-parser along with a run-time interpreter. I believe the option I favored has significant advantages: Generating source code from info files.

The following section describes the idea, along with some (pseudo)code. The description is not exhaustive, but sufficiently thorough to ensure that the idea and execution is clear.

## Compile-to-code

---

It is surprisingly easy to generate source code. Especially for well-structured and fixed inputs such as info files. As a pre-computation step the system reads through info files and generates the necessary Turbo Pascal source code from them. To illustrate, my info files contain the information for skills: Their name, description, cost, requirements and effects. In my project these are described by a construct along this line (in pseudocode)

```text
TSkill = record
  name : string;
  cost : integer;
  desc : procedure;
  reqs : function : boolean;
  effs : procedure;
end;
```

Here is an example of what an entry in an info file may contain, format:

```text
"skillstart" <name>
"cost" <value>
"description" <value>
  <string>
  ..
  <string>
"requirements" <value>
  <req>
  ..
  <req>
"effects" <value>
  <eff>
  ..
  <eff>
"skillend"
```

Example:

```text
skillstart Greater Melee Skill
cost 25
description 2                                # 2 means two lines of description follow
  Greater melee skill enables heroes to fight with greater efficiency.
  Their melee attacks are more likely to hit and deal more damage.
requirements 2                               # 2 means two requirements follow
  has  Basic Melee Skill                     # checks if character has "Basic Melee Skill"
  test ( ch_strength + ch_dexterity ) > 32   # checks if character strength + dexterity exceeds 32
effects 3                                    # 3 means three effects follow
  eff ch_to_hit 4
  eff ch_damage 2
  eff ch_max_HP ( ( ch_strength + ch_dexterity ) * 2 ) div 5
skillend
```

This input generates source code that looks as follows:

```text
InitSkill('Greater Melee Skill', 25, description14, requirements14, effects14);

  ..

procedure description14;
begin
  writeln('Greater melee skill enables heroes to fight with greater efficiency.');
  writeln('Their melee attacks are more likely to hit and deal more damage.');
end;

function requirements14 : boolean;
var
  tmp : boolean;
begin
  tmp := true;
  tmp := tmp and (HeroHas('Basic Melee Skill'));
  tmp := tmp and ((GetValue(ch_strength)+GetValue(ch_dexterity))>32);
  requirements14 := tmp;
end;

procedure effects14;
begin
  AddValue(GetPointer(ch_to_hit), 4);
  AddValue(GetPointer(ch_damage), 2);
  AddValue(GetPointer(ch_max_HP), ((GetValue(ch_strength)+GetValue(ch_dexterity))*2)/5);
end;
```

Naturally this requires the existence of "InitSkill", "HeroHas()", "GetValue()" and "AddValue()". These are fortunately quite straightforward. The rule during code generation of expressions such as "( ch_strength + ch_dexterity ) > 32" is quite simple: Any part that starts with a letter ("ch_strength" and "ch_dexterity") are output as "GetValue(ch_strength)" and "GetValue(ch_dexterity)", all others are output verbatim.

A side-note: The suffix "14" in the names of procedures/functions above reflects that it is the 14th skill read in by the system. The next skill would be noted with the suffix "15", etc.

An exerpt from the source-code-generator:

```text
procedure HandleEffects(d : integer);
{ scans through next "d" lines of info file }
var
  a : integer;
  s, t : string;
begin
  writeln(h, 'procedure effect' + idstr + ';';
  writeln(h, 'begin');
  a := 0;
  while a < d do begin
    readln(f, s);
    s := TrimRight(TrimLeft(RemoveComments(s)));
    while length(s) > 0 do begin
      t := GetIdenfitifer(s);      { removes the first non-whitespace text from s and returns it into t }
      s := TrimRight(TrimLeft(s));
      write(h, '  AddValue(GetPointer(' + t + '), ');
      while length(s) > 0 do begin
        t := GetIdentifier(s);
        s := TrimRight(TrimLeft(s));
        if isLetter(t[1]) then begin
          write(h, 'GetValue(' + t + ')');
        end else begin
          write(h, t);
        end;
      end;
    end;
    writeln(h, ');');
    inc(a);
  end;
  writeln(h, 'end;');
end;
```

## Advantages / Disadvantages

---

The info file variant described here is probably the very fastest solution for info files, as the data is never read-in, interpreted, script-executed, or otherwise manipulated at run-time. It is simply executed. Furthermore: it is a very compact solution as compiled code tends to be considerably more dense than raw text.

Additionally, any functions that the programming environment caters for can be implemented effortlessly into info files. Expressions may be of arbitrary complexity and yet remain effortless to handle, since the compile-to-code simply translates the info file into source code. The really hard work is handled by the programming environment. Extras include the ability of good compilers to optimize the code. In the above example the "_2 / 5" is implemented as "_ 0.4" in good optimizers.

The disadvantage is that it isn't possible to merely change some info file details to change the game, the new info files always have to be recompiled into the project to reflect info file changes.
