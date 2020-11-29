---
title: ecco the dolphin
slug: ecco-the-dolphin
publishDate: 2019-11-29T02:47:27.000Z
lastmod: 2019-12-01T06:13:10.000Z
tags: [Experiments, p5.js]
description: Let's recreate this weird effect from Ecco the Dolphin.
images:
- /images/2019/11/background.png
---
<script src="https://unpkg.com/p5@^0.10/lib/p5.min.js"></script>
<script>
    // Ecco effect v2 by juju2143
    // based on original code by foone
    // https://github.com/foone/ecco-distortion-effect
    // Licenced under GPL3

    var shifts = "gDkZcB510pQ:3pSf]>4oLh\>41pS;nWd_@lYcB5oT;nJ82pSfCkH7nV<3oL82pN9nV<31pNh\c_?lH7nKiE6210000pOg\cB5oL82pN931pOg\>mWe^aA5110pO9nKh\bAlYdCkG7oL8nVe^a`@5oL82pSf\>lXd^?lX=3oM9nV<31pNhEjF6oL8nJiEjF6oU;310pQ:3pSfC6oU;mW=41pN9nK8nJ8nV;nV<310000pPgD6oUe]>4oL9nVe^a`@";

    function preload() {
        img = loadImage('/images/2019/11/background.png');
    }

    function setup() {
        var c = createCanvas(320, 224);
        c.parent('sketch-holder');
    }

    function draw() {
        for (var i = 0; i < height; i++)
            image(img, 0, i, width, 1, shifts.charCodeAt((i + frameCount) & 0xFF)-0x30, i, width, 1);
    }
</script>

&nbsp;

<div id="sketch-holder"></div>

Welcome to the second installment of my code experiments! This time we're gonna look into the weird effect used in the background of messages in Ecco the Dolphin for the Sega Genesis (or MegaDrive, if you're asking someone outside the Americas). I got the idea from Twitter user @Foone who helpfully reverse engineered the game ROM (with Twitter user @Reaper_man02) to figure out how it works and wrote an implementation in Python. Then I went ahead and adapted it in p5.js.

{{< twitter 1199590443538796549 >}}
Read the replies for some explanations.

Turns out it's pretty simple: there's a table in ROM, it's basically how much each line should be shifted in the x axis. Then for each frame we shift the values around so it looks like it's scrolling.

![](/images/2019/11/background.png)
Base image.

```js
function generate_for_offsets(offset) {
    var SCREEN_SHIFTS = [
        55, 20, 59, 42, 51, 18, 5, 1, 0, 64, 33, 10, 3, 64, 35, 54, 45, 14, 4, 63,
        28, 56, 44, 14, 4, 1, 64, 35, 11, 62, 39, 52, 47, 16, 60, 41, 51, 18, 5,
        63, 36, 11, 62, 26, 8, 2, 64, 35, 54, 19, 59, 24, 7, 62, 38, 12, 3, 63,
        28, 8, 2, 64, 30, 9, 62, 38, 12, 3, 1, 64, 30, 56, 44, 51, 47, 15, 60, 24,
        7, 62, 27, 57, 21, 6, 2, 1, 0, 0, 0, 0, 64, 31, 55, 44, 51, 18, 5, 63, 28,
        8, 2, 64, 30, 9, 3, 1, 64, 31, 55, 44, 14, 61, 39, 53, 46, 49, 17, 5, 1, 1,
        0, 64, 31, 9, 62, 27, 56, 44, 50, 17, 60, 41, 52, 19, 59, 23, 7, 63, 28, 8,
        62, 38, 53, 46, 49, 48, 16, 5, 63, 28, 8, 2, 64, 35, 54, 44, 14, 60, 40,
        52, 46, 15, 60, 40, 13, 3, 63, 29, 9, 62, 38, 12, 3, 1, 64, 30, 56, 21, 58,
        22, 6, 63, 28, 8, 62, 26, 57, 21, 58, 22, 6, 63, 37, 11, 3, 1, 0, 64, 33,
        10, 3, 64, 35, 54, 19, 6, 63, 37, 11, 61, 39, 13, 4, 1, 64, 30, 9, 62, 27,
        8, 62, 26, 8, 62, 38, 11, 62, 38, 12, 3, 1, 0, 0, 0, 0, 64, 32, 55, 20, 6,
        63, 37, 53, 45, 14, 4, 63, 28, 9, 62, 38, 53, 46, 49, 48, 16
    ]
    var output_shifts = [];

    for (var current_line = 0; current_line < height; current_line++) {
        output_shifts[current_line] = SCREEN_SHIFTS[(current_line + offset) & 0xFF];
    }
    return output_shifts;
}
```
p5.js code for the above canvas, part 1

This function basically computes a table of offsets for the current frame by adding the line number with the frame number, mod 256. Then a bit of initialization code:

```js
function preload() {
    img = loadImage('https://toasters.rocks/images/2019/11/background.png');
}

function setup() {
    createCanvas(320, 224);
}
```
p5.js code for the above canvas, part 2

And now this is where it gets interesting. For each line we call the `image` function which crops a 320x1 portion of the image with the appropriate x offset we calculated earlier. Note that the original image is 384x224. 

```js
function draw() {
    var adjusts = generate_for_offsets(frameCount);
    for (var i = 0; i < height; i++)
        image(img, 0, i, width, 1, adjusts[i], i, width, 1);
}
```
p5.js code for the above canvas, part 3

We can also completely forego the `generate_for_offsets` function and make it simpler:

```js
var shifts = [
    55, 20, 59, 42, 51, 18, 5, 1, 0, 64, 33, 10, 3, 64, 35, 54, 45, 14, 4, 63,
    28, 56, 44, 14, 4, 1, 64, 35, 11, 62, 39, 52, 47, 16, 60, 41, 51, 18, 5,
    63, 36, 11, 62, 26, 8, 2, 64, 35, 54, 19, 59, 24, 7, 62, 38, 12, 3, 63,
    28, 8, 2, 64, 30, 9, 62, 38, 12, 3, 1, 64, 30, 56, 44, 51, 47, 15, 60, 24,
    7, 62, 27, 57, 21, 6, 2, 1, 0, 0, 0, 0, 64, 31, 55, 44, 51, 18, 5, 63, 28,
    8, 2, 64, 30, 9, 3, 1, 64, 31, 55, 44, 14, 61, 39, 53, 46, 49, 17, 5, 1, 1,
    0, 64, 31, 9, 62, 27, 56, 44, 50, 17, 60, 41, 52, 19, 59, 23, 7, 63, 28, 8,
    62, 38, 53, 46, 49, 48, 16, 5, 63, 28, 8, 2, 64, 35, 54, 44, 14, 60, 40,
    52, 46, 15, 60, 40, 13, 3, 63, 29, 9, 62, 38, 12, 3, 1, 64, 30, 56, 21, 58,
    22, 6, 63, 28, 8, 62, 26, 57, 21, 58, 22, 6, 63, 37, 11, 3, 1, 0, 64, 33,
    10, 3, 64, 35, 54, 19, 6, 63, 37, 11, 61, 39, 13, 4, 1, 64, 30, 9, 62, 27,
    8, 62, 26, 8, 62, 38, 11, 62, 38, 12, 3, 1, 0, 0, 0, 0, 64, 32, 55, 20, 6,
    63, 37, 53, 45, 14, 4, 63, 28, 9, 62, 38, 53, 46, 49, 48, 16
]

function preload() {
    img = loadImage('https://toasters.rocks/images/2019/11/background.png');
}

function setup() {
    createCanvas(320, 224);
}

function draw() {
    for (var i = 0; i < height; i++)
        image(img, 0, i, width, 1, shifts[(i + frameCount) & 0xFF], i, width, 1);
}
```
p5.js code for the above canvas, version 2

Now that huge array is a bit unwieldy, maybe you can compress it? Sure thing.

```js
console.log(String.fromCharCode(...shifts.map(x => x + 0x30)));
```
Let's transform that stupid array into something better

```js
var shifts = "gDkZcB510pQ:3pSf]>4oLh\>41pS;nWd_@lYcB5oT;nJ82pSfCkH7nV<3oL82pN9nV<31pNh\c_?lH7nKiE6210000pOg\cB5oL82pN931pOg\>mWe^aA5110pO9nKh\bAlYdCkG7oL8nVe^a`@5oL82pSf\>lXd^?lX=3oM9nV<31pNhEjF6oL8nJiEjF6oU;310pQ:3pSfC6oU;mW=41pN9nK8nJ8nV;nV<310000pPgD6oUe]>4oL9nVe^a`@";

function preload() {
    img = loadImage('https://toasters.rocks/images/2019/11/background.png');
}

function setup() {
    createCanvas(320, 224);
}

function draw() {
       for (var i = 0; i < height; i++)
           image(img, 0, i, width, 1, shifts.charCodeAt((i + frameCount) & 0xFF)-0x30, i, width, 1);
}
```
p5.js code for the above canvas, version 3

From there, there's a lot of tricks to compress your code so it goes a bit faster. With that much code, we went from something that looks complex into something simple, which is pretty cool. Well, that's all for today, hope you learned a bit with that :)

All content owned by their respective owners: game, data and assets by [Novotrade International](https://en.wikipedia.org/wiki/Appaloosa_Interactive), code by Foone and adapted by myself licenced under GPL3.
