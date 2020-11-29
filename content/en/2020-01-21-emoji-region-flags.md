---
title: Emoji region flags
slug: emoji-region-flags
publishDate: 2020-01-21T20:36:56.000Z
lastmod: 2020-01-21T20:40:28.000Z
tags: [Experiments]
description: All of the emoji flags in one convenient font?
images:
- /images/2020/01/CA-QC-2.png
---

For this project, I've been inspired by the [#emojibzh](https://www.emoji.bzh) movement which attempts to include the flag of the Brittany region of France in the emoji repertoire. So, I've been looking at it and here's what I found.
<!--more-->
So far, Unicode does allow for regional flags: while for instance the United Kingdom would be represented as a combination of Regional Indicator Symbol Letter G (U+1F1EC) plus Regional Indicator Symbol Letter B (U+1F1E7) (which is its ISO 3166-1 alpha-2 code made out of Regional Indicator Symbol Letters), Scotland would be a Black Flag (U+1F3F4), its ISO 3166-2 code made out of Tag Latin Small Letters (?U+E0067, U+E0062, U+E0073, U+E0063, U+E0074, spelling out "GBSCT") and finally a Cancel Tag (U+E007F). That's fairly complicated, but it works. This way, every region, state or province of every country that has a flag would theorically be represented.

So if I want the flag of Québec, Canada, where I live, theorically the code would be U+1F3F4 (the black flag), U+E0063, U+E0061, U+E0071, U+E0063 (CAQC), U+E007F (the cancel tag), right? It is valid, yet it won't work. Yet, at the time I write those lines. That's because this sequence is not part of RGI (Recommended For General Interchange), which means vendors don't have to support it (and in practice unless the entire Internet is asking about it it's not supported at all). So far, only 3 subdivisions are part of RGI, namely England, Scotland, and Wales, all added in 2017. (Interestingly, Northern Ireland could also be part of that club, but their flag never was official.)

So what gives? There's some proposals for RGI, namely [L2/17-382](https://www.unicode.org/L2/L2017/17382-rgi-flag-omnibus.pdf) and others that aren't public yet but are [known to be forwarded to the UTC](https://www.unicode.org/emoji/emoji-requests.html), but looks like they're in limbo so far. I guess vendors (such as Google, Facebook, Twitter and other companies who made emoji fonts for their messaging apps) don't want to litter their fonts and emoji pickers with too much flags.

I believe we should at least have one emoji font with all (or most) of them, so I've been looking at it and I found out the Noto font is [open source](https://github.com/googlefonts/noto-emoji) and comes with Python scripts to make it very easy to add more emojis to it. And guess what, the repository includes all flags for the provinces of Canada, the US states, Mexico states, and Northern Ireland, but they're disabled by default! So I did the thing and I enabled them (very easy edit in the Makefile), added Catalonia and Bretagne for good measure and generated a new font with them. The scripts makes a beautiful stylization out of the flags they pulled from Wikipedia.

![](/images/2020/01/CA-QC.png)

![](/images/2020/01/FR-BRE-1.png)

![](/images/2020/01/ES-CT.png)

![](/images/2020/01/GB-NIR.png)

There you go, #emojibzh, here's your emoji in Google style. Clockwise from top left, Québec (CA-QC), Bretagne (FR-BRE), Northern Ireland (GB-NIR), Catalonia (ES-CT)
You can find the modified GitHub repository with new flags [right here](https://github.com/juju2143/noto-emoji) where you can comment on it and you can download the latest build of the font [here](https://github.com/juju2143/noto-emoji/raw/master/fonts/NotoColorEmoji.ttf) to install on your computer and get all of the flags if you need them. You can test them on Emojipedia on pages like [here](https://emojipedia.org/flag-for-bretagne-frbre/) and [here](https://emojipedia.org/flag-for-quebec-caqc/) (I still haven't figured out how to make a test page but I guess that will come later), but unfortunately, it's not going to work on most apps like Twitter, Facebook, Discord and the like, since tons of emojis are added to the Unicode standard every year, they can't guarantee the same emojis are supported on every computer and so they prefer coming up with their own font so they have a consistent look across their network. You still can view them on web pages, I guess that's a good start, but that's why it's still important to support RGI proposals and to keep tweeting about them :)

On that, have fun with them! ??????
