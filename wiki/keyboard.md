# Keyboard

---

Most roguelikes use the keyboard to receive input from the [player](player.md). Even though [mouse](mouse.md) driven roguelikes have seen an increase in popularity, as the number of graphical roguelikes in a windowed environment has increased, the keyboard is still the dominating way of playing roguelikes.

There is no standardized way of keyboard control for roguelikes, but there are a few [preferred controls](preferred_key_controls.md).

Roguelikes generally model the keyboard to be in a similar repeating key mode as with entering text, where pressing down a key will cause one instant keypress event and then a series of repeated events in short succession after a longer initial delay. Good default delay times (taken from the SDL library) are 500 milliseconds between the first and second keypress events from the key held down, and 30 milliseconds between the subsequent repeated events while the key is held down.
