---
title: "Twitter tips and tricks I'm reasonably sure I came up with but I kinda wish more people should use"
slug: twitter-tips
publishDate: 2022-10-11T21:00:00.000Z
lastmod: 2022-10-14T21:00:00.000Z
tags: [Rant]
description: "Warning: Those are kinda involved and features characters you won't find on your keyboard. If you're too lazy to fetch them from a random website I don't blame you I guess"
---

## 1. How to evade bots and algorithmic censorship

Use [Mathematical Alphanumeric Symbols](https://en.wikipedia.org/wiki/Mathematical_Alphanumeric_Symbols) and you'll be none the wiser.

Example: 𝖼𝗋𝗒𝗉𝗍𝗈 𝖭𝖥𝖳

It's indisguishable from basic ASCII, except if you're blind and you use a screen reader. In which case I hope you won't mind for the sake of avoiding bots.
Which is why _Mathematical Alphanumeric Symbols_ has made a lot of people very angry and been widely regarded as a bad move. If possible, only use them for this exact use case.

## 2. Mention usernames without spamming them with notifications

Some people do for instance `@.juju2143` or `@/juju2143` to avoid sending useless notifications to someone on a tweet they know is going to be very popular or they don't want you to see because they hate you but that's kinda weird (also I don't ever mind if you don't do that send them notifications away ;))

Instead, wedge a zero-width space between the @ and the username! Impress your crowd! How do you do that! Here's a few characters that should work:

- `U+200B` _Zero-width space_
- `U+200C` _Zero-width non-joiner_
- `U+200D` _Zero-width joiner_
- `U+2060` _Word joiner_
- `U+FEFF` _Zero-width no-break space_

Example: [@&#x200B;juju2143](https://twitter.com/juju2143/status/1579984904103677953)

Again, indistinguishable from a normal mention, except no link, but it's also hard to copy and paste and will most likely break a lot of stuff, so use cautiously.

Have fun!