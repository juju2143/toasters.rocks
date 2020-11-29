---
title: trippy perlin noise
slug: trippy-perlin-noise
publishDate: 2019-11-25T18:16:14.000Z
lastmod: 2019-11-29T01:06:04.000Z
tags: [Experiments, p5.js]
description: "Warning: contains some intensive JavaScript that might break your computer"
images:
- /images/2019/11/perlin.png
---
<script src="https://unpkg.com/p5@^0.10/lib/p5.min.js"></script>
<script>
function setup() {
  var c = createCanvas(256, 256);
  c.parent('sketch-holder');
  background(0);
}

function draw() {
  var n = 0;
  var z = 1;
  var speed = 0.005;
  
  noiseDetail(8, 0.65);
  loadPixels();
  for(a=0; a<3; a++)
  {
    noiseSeed(a+n);
    for(i=0; i<width*height*4; i+=4)
    {
      x = ((i/4)%width)/width*z;
      y = floor((i/4)/width)/height*z;
      pixels[i+a] = noise(x,y,frameCount*speed)*255;
    }
  }
  updatePixels();
}
</script>

&nbsp;

<div id="sketch-holder"></div>

Now that I can easily embed JavaScript on this blog, I should put here some of my experiments and weird code that does nothing, cause why not. This one is some colored Perlin noise made using p5.js, it would be pretty cool as a background for something, isn't it?

It is quite simple using the `noise` function, one the simplest things you can do with it is `color = noise(x, y, time)` for each pixel on the canvas, usually you make it grayscale but this time I'm generating 3 of them for each color component (red, green, blue) with a different seed. It looks pretty cool, if you ask me.

```js
function setup() {
  createCanvas(256, 256);
  background(0);
}

function draw() {
  var n = 0;
  var z = 1;
  var speed = 0.005;
  
  noiseDetail(8, 0.65);
  loadPixels();
  for(a=0; a<3; a++)
  {
    noiseSeed(a+n);
    for(i=0; i<width*height*4; i+=4)
    {
      x = ((i/4)%width)/width*z;
      y = floor((i/4)/width)/height*z;
      pixels[i+a] = noise(x,y,frameCount*speed)*255;
    }
  }
  updatePixels();
}
```
p5.js code for the above canvas.

On that, I recommend [p5.js](http://p5js.org), or its big sister [Processing](https://processing.org), if you want to learn code and do some cool artsy stuff with it, I've been recommending to a bunch of people lately :) If you want to learn and you're a complete beginner, there's a series about that on [The Coding Train](https://www.youtube.com/playlist?list=PLRqwX-V7Uu6Zy51Q-x9tMWIv9cueOFTFA) on YouTube.

(P.S.: Sorry if I break your computer by executing some intensive JavaScript on this post! The above image isn't an image but the actual code running in your browser, and the whole calculation is a bit intensive, so I kept the canvas size rather small so it's bearable on older computers like mine :))

Edit (26/11): Updated the post with code and some explanations.
