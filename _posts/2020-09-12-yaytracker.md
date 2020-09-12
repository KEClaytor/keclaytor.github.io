---
layout: post
title: YAY Tracker
subtitle:
author: K. E. Claytor
tags: programming, electronics, kids projects, circuitpython, micropython, python, project, easy, diy, improvement, time tracking
hero_image: /assets/images/yaytracker/hero.jpg
---

# The YAY Tracker

So I've been mostly enjoying pandemic work-from-home.
The one thing I don't like though is the easy access to snacks.
So to try to reduce my snacking consumption, I thought I would try to replace the instant gratification of a snack with something else.

Thus was born the "YAY Tracker" - instead of going for a snack, I can click "YAY" (A button) and get some motivational support!
If I can't refuse temptation and end up getting the snack, I can click the "NAY" (C button) and get some support for next time.
The total counts of "YAY"s (snack avoided) and "NAY"S (whoops snacked) are logged throughout the day and can be accessed (B button).

Here's what it looks like:

![YAY Tracker in action](/assets/images/yaytracker/yaytracker.gif)

This project also appears at:
- [Github](https://github.com/KEClaytor/YAYTracker)

This project costs $43 and is easy:
- Hardware: Easy
- Software: Easy
- Assembly: Easy

# Hardware

The electronics [parts list](http://www.adafruit.com/wishlists/510219) consist of:
- Feather M0
- Featherwing OLED 128x32
- Lithium Ion Battery 400 mAh

# Wiring & Assembly

Solider on some [short headers](https://www.adafruit.com/product/2940) or [stacking headers](https://www.adafruit.com/product/2830) to the feather, snap on the LCD featherwing, sandwiching in the battery, upload the code, and you're good to go.

# Code

The full source is on [Github](https://github.com/KEClaytor/YAYTracker), I re-used the button code from the [multipad project](https://github.com/KEClaytor/multi-pad).

There's nothing too fancy about it - pull a random message from a list, increment a counter, keep track of display levels.
Regarding the dispaly, the [Adafruit displayio has an excellent tutorial](https://learn.adafruit.com/circuitpython-display-support-using-displayio/introduction).

# Conclusion

This was a fun introduction to the adafruit drawing library, but ultimately it did not help me in my snacking goals.
Some improvements would be longer battery life to where it could go the whole day on a charge, and storage of the yay/nay counts so they don't reset when power is lost.
