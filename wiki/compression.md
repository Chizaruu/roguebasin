# Compression

---

Roguelike games often need to save data. Sometimes the data takes up a large amount of space, and often is repetitive. There are several methods to solve this problem. Some of them are listed below.

## Run-length Encoding

---

A simple solution is run-length encoding (RLE). It compresses "runs" of data, such as "aaaaaabbbb". These runs can often be created when saving the dungeon. The save file is usually like this: "#######....@.##########", with many walls and floors in a row.

RLE stores a "count" byte and a "data" byte. The count byte stores the number of times the data byte is repeated. Because the count can never by 0, we can subtract one from the count byte to compress extremely long runs of data slightly more.

RLE is a simple but limited compression method. Much more effective methods exist, but they are harder to implement. There are several libraries that will compress files for you (such as zlib).

Here is example code for using run-length encoding (using Visual Basic.net):

```vbnet
Function RLEEncode(ByVal Bytes As Byte()) As Byte()
    Dim PreviousByte As Byte = Bytes(0)
    Dim ByteCount As Byte = 0
    Dim OutputBytes As New System.Collections.Generic.Queue(Of Byte)
    For CurrentByte As Integer = 1 To Bytes.Length - 1
        If Bytes(CurrentByte) = PreviousByte And ByteCount < 255 Then
            ByteCount = CByte(ByteCount + 1)
        Else
            OutputBytes.Enqueue(ByteCount)
            OutputBytes.Enqueue(PreviousByte)
            PreviousByte = Bytes(CurrentByte)
            ByteCount = 0
        End If
    Next
    OutputBytes.Enqueue(ByteCount)
    OutputBytes.Enqueue(PreviousByte)
    Return OutputBytes.ToArray
End Function

Function RLEDecode(ByVal Bytes As Byte()) As Byte()
    Dim OutputBytes As New System.Collections.Generic.Queue(Of Byte)
    For CurrentByte As Integer = 0 To Bytes.Length - 1 Step 2
        For I As Integer = 0 To Bytes(CurrentByte)
            OutputBytes.Enqueue(Bytes(CurrentByte + 1))
        Next
    Next
    Return OutputBytes.ToArray
End Function
```

A more efficient way of RLE is to use some kind of token for identifying repetitive data.

`abcaaacba => abc<token>a3cba`

To bypass the use of a token simply double the repetetive character and add a "countbyte".

`abcaaacba => abcaa3cba`

a complete delphi-class can be found here <http://svn.berlios.de/wsvn/declacol/trunk/classes/class_rle.pas>

## Bitwise Storage

---

Most roguelikes also have a lot of different monsters, or other adversaries which have different states and abilities. Some can become hungry, other freeze on touch, are immune to almost everything, or are just a little scared of light.

A lot of these states are either on or off. So a NPC, is either hungry or not. Normally, you would use a Boolean for this. Sadly, in most languages a large list of booleans can be a bit costly. You could save these states as bits in a larger byte, or an integer.

For example: Consider the list of abilities, IMMUNE_INSTANT_DEATH, IMMUNE_STONING, IMMUNE_POISON, ALIVE, THIEF, AMORPHOUS, LOCKPICKER, SCARY, HORRIBLE, MADNESS_INDUCING. These could all be stored in a single integer. Just give the different abilities the value of a bit. So the first ability would get 1, the second 2, the third 4. All the way up to MAX_INT.

| Ability              | Value | Byte       |
| -------------------- | ----- | ---------- |
| IMMUNE_INSTANT_DEATH | 1     | 0000000001 |
| IMMUNE_STONING       | 2     | 0000000010 |
| IMMUNE_POISON        | 4     | 0000000100 |
| ALIVE                | 8     | 0000001000 |
| THIEF                | 16    | 0000010000 |
| AMORPHOUS            | 32    | 0000100000 |
| LOCKPICKER           | 64    | 0001000000 |
| SCARY                | 128   | 0010000000 |
| HORRIBLE             | 256   | 0100000000 |
| MADNESS_INDUCING     | 512   | 1000000000 |

So, if we would have a Kobold Thief, it would be Alive, be a Thief, and have the Lockpicker ability. 8 + 16 + 64 = 88, or 0001011000. While a Lich would have, 1 + 2 + 4 + 128 = 135, or 0010000111. As he is not alive, and is immune to almost everything, and certainly scary. And a [Shoggoth](http://en.wikipedia.org/wiki/Shoggoth) would be 4 + 8 + 32 + 512 = 556, or 1000101100.

And all these different abilities would only take the space of a single integer. And there is even space to spare.

If you implement this kind of bitwise ability storage, you can use bitwise operators to efficiently get the different abilities. For more information on bitwise operators, please look at the [excellent tutorial made by Stefan Bird](http://goanna.cs.rmit.edu.au/~stbird/Tutorials/BitwiseOps.html)

## Words of Warning

---

While it can be fun to optimize your compression algorithms to save as much space as possible, it shouldn't be done unless you actually have a need to save space. Hard drives have a large amount of space available, so compression isn't as important as it used to be (except for images, movies, etc). It is important if you are developing for a limited platform such as a mobile phone, though.

Sometimes all the invested energy doesn't really improve your roguelike. Compression is a trade-off between speed and storage space. (When you compress, you trade speed and clock cycles for storage space). If you are really worried about speed and data usage, use some profilers made for this purpose.

If you start to make your code unreadable just to save a few bytes of memory or hard drive space, you have probably gone too far.
