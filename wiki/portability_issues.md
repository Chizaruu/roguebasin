# Portability issues

---

The purpose of this article is to collect portability issues often encountered by roguelike programmers as well as possible solutions for these problems.

## Memory

---

- Some [Operating systems](platforms.md), such as IRIX, allow the programs to read from a NULL address, while others will segfault instantly. Trying to write to a NULL address will hopefully always cause a segfault. (Except on many embedded systems. The Gameboy Advance, for example, will not segfault on a NULL address write.)

## Display

---

- Only the characters of the basic (7bit) ASCII character set are reasonably portable. Using characters from extended character sets (like the solid blocks from the IBM DOS character set) or characters which aren't classified as printable by the ASCII standard (like the smileys from IBM DOS) will probably cause problems.
  - Extensions to ASCII are not portable. Unicode is portable, though, plus it includes all the old IBM DOS characters, though not necessarily at the same code points.
- [Output libraries](output_libraries.md) discusses the options you have.

## Filesystem

---

- Some [Operating systems](platforms.md) (such as any that derive from or pattern [UNIX](unix.md) or follow POSIX standards) are case-sensitive as far as filenames are concerned. Others (like [DOS](dos.md) or [Windows](windows.md)) are case-insensitive. Still others (like HFS , in common use on [OS X](mac_os_x.md)) preserve case but are insensitive to it. One shouldn't rely on any one case behavior to achieve maximum portability.
- There are [Operating systems](platforms.md) that forbid use of certain characters within file names, most often path separators (e.g., colons in Classic [Mac OS](mac.md)). Other characters may require "escaping" in filename references.
- [Operating systems](platforms.md) may also restrict filename length or the total length of an absolute filepath.
- [DOS](dos.md) and [Windows](windows.md) use backslash as their path separator while [UNIX](unix.md) uses slash. Most Windows APIs will understand the forward slash as a path separator, however.

## Sockets / Networking

---

If you use socket-related system calls directly, you will need to change your code a bit for different platforms:

- [Windows](windows.md) uses different header files under [C](c.md)/[Cpp](c++.md). The following will work under mingw32.

```text
   #ifdef WIN32
       #include <winsock2.h>
       typedef int socklen_t;
   #else
       #include <sys/socket.h>
       #include <netinet/in.h>
       #include <arpa/inet.h>
       #include <netdb.h>
   #endif
```

- You will need to call the following before any standard socket code:

```text
   WSADATA wsaData;
   WSAStartup(MAKEWORD(2,2), &wsaData);
```

- And the following on exit:

```text
   WSACleanup();
```

- And as a handy hint, sleep:

```text
   #include <windows.h>
   #define sleep(n) Sleep(1000 * n)
```

However, it is often a good idea to use a higher-level interface, such as SDL_net, instead. These are easier to work with and more portable.
