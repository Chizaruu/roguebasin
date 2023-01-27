# Fast Light Toolkit:Reviews

---

## Review of [Fast Light Toolkit](fast_light_toolkit.md) by PaulBlay

---

### Usage background

Preferred IDE : Visual C++ 2008 Express Edition

Preferred Platform : Windows XP

Other requirements sought : Unicode support / Works with SQLite

### Experience with Fast Light Toolkit

I had some initial problems getting the Hello World project to compile with FLTK. This was due to an omission in the [online instructions](http://www.fltk.org/doc-1.3/basics.html). When compiling with VC++ you need to explicitly define WIN32 (e.g. /DWIN32 in the C/C++ compiler command line). Also where it says COMCTRL32.LIB it should say COMCTL32.LIB. Having sorted those points out I actually got my "Hello World" displayed! The functions provided by FLTK appear refreshingly simple to understand and I'm hopeful I'll be able to do what I need to with it.

### Conclusion

I have confirmed that Japanese text can be displayed via UTF-8 with FLTK functions. It is a little hard to get used to - but I think that's just because I'm not used to C++. The FLUID tool (once I noticed it) is a neat way to generate initial code and I'm hopeful I'll get the hang of it soon. Although [Qt](qt.md) looks more professional this approach appears to be more flexible and may well pay off better in the long run.
