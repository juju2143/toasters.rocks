---
title: "Toasters Rocks 4.0"
slug: toasters-rocks-4
publishDate: 2020-11-28T11:16:57-05:00
date: 2020-11-28T11:16:57-05:00
lastmod: 2020-11-28T21:15:50-05:00
tags: 
- Meta
description: Brand new blog, again.
images:
- /images/2020/11/bg.png
---

Well, it seems to be a tradition around this time of the year, every 368 days or so. A brand new blog. But this time, the story is quite interesting.

### Genesis

The first iteration of this blog was basically my Tumblr blog. By November 2018, I became largely disinterested by Tumblr due to various reasons, and I found this blog engine called [WriteFreely](https://writefreely.org), written in Go, could do federation so the blog posts would show up in people's Mastodon feeds, effectively replicating some of Tumblr's social features. It looked pretty cool at first, but it was really beta software at best, didn't really work fantastically, and I had to run the binary in the background. But eh, I had a pretty nice minimalistic design.

Later in November 2019, I was messing with some Plesk-based web hosting account I got for free with a cheap VPS (which hosted the blog, and later died), I discovered it could do Node.js hosting, so I decided to install [Ghost](https://ghost.org), just to try out if it could work, and if it could be a nice solution if I ever run out of money for the VPS. Of course, being Node.js, it's a bit finicky and it expects you to run it on an actual VPS and not on some Plesk-based that barely could run `npm install`, but eh, it worked. Kind of. The server stopped when it's idle until there's a new request, so if it's been a while no one visited the site you'd be greeted with a "The blog is starting up" message, people are idiot enough to not get you'd have to come back later and then message me the blog is broken, in which I'll just tell them, just try again... Anyway, it worked for me, since it was free. Until they upgrade their version of Node.js or something, maybe.

### And this is where it breaks for good

Around November 2020, said hoster moved their servers closer to Paris, France where most of their clients are, apparently. (I'm not even anywhere near Paris, so eh, probably not going to be significantly faster for me.) Downtime expected, change your IPs, the usual for a small host. From what I see, the migration went quite well, and I hope they had a nice roadtrip since they had to physically move servers from about 4 hours drive away from Paris. But what they didn't say, unbeknownst to me, is that they axed the Node.js option in their Plesk... I complained to their Discord server and they said something along the line of it was only getting used by people uploading huge scripts and abusing the available resources. Well, bummer. At least the files and database are still there.

### Time for another engine?

Why, of course. My goal still was about building something better than WordPress. Something self-hosted, simple to maintain, very customizable, and faster than the humongous PHP script WordPress is. Which is not that hard, really. WriteFreely, while it embraces minimalism, was too minimalistic for my needs, Ghost, while it's 17x faster than WordPress and way closer to my needs, still wasn't cutting it, and Tumblr obviously shot themselves in the foot and is a prime example why you don't want to go self-hosted.

So, why not a static site generator? Back then I was also messing with Pandoc, which basically converts Markdown files to HTML and I thought I could use that. I quickly realized that would basically be reinventing the wheel, so I asked around. A friend of mine recommended [Hugo](https://gohugo.io), which is perfect for my needs.

Now the plan is:

1. Build a new theme for Hugo (because I can)
2. Export the posts from my Ghost install (which is ironically now only a ghost of itself)
3. Import them into Hugo
4. ????
5. PROFIT

### Build the theme

Ah, I didn't really do that since my Tumblr days. I did changed the font a bit on the WriteFreely default theme, and I started a theme for Ghost a while ago based on the Windows 95 UI, but it never went anywhere. But this time, didn't even looked at the default themes and starting porting that one I did for [my bio page](http://juju2143.ca). And, yeah, I really like Hugo. In all, it took me about a week of free time or so. Looks pretty cool, isn't it?

### Export the data

Now that's the hardest part. I kinda wish here they'd give at least a headstart before they took it down so I could export the data, but eh. If you know Node.js you'd probably know if you leave some code unmaintained for more than 6 months something will surely break in a later major version of Node.js. And sure enough, something wasn't compatible with Node 15. So I dusted off my old copy of Node 12, and now I had to figure out how the config file worked because I somehow configured it with environment variables like an idiot and now they're gone. I also attempted to import the database dump into SQLite, but it's too different from MySQL and I ended up installing a MariaDB server.

Now that everything is working somewhat, I could access the old blog from localhost and hit the export button.

### Import the data

Now you'll want to convert your backup file to a bunch of Markdown files. `ghost-to-md` is a tool that does exactly that:

```sh
npm install -g ghost-to-md
ghost-to-md yourwebsitebackup.json
```

Now you'll want to dump the resulting Markdown files in the content folder, the images from the old Ghost install to the static folder, fix front matter variables to the correct name, fix image URLs (both of which can be done with search and replace), and there you go, a nice website.

### Aftermath

Now I need to fix the posts one by one, since the converter didn't do a very good job at converting stuff, in particular header images, embeds and tables, plus that script wasn't written specifically for Hugo, and there's a few bugs to squash here and there, but eh, since I don't have a ton of posts it's kinda doable. It's still gonna be wonky a bit at first but I hope you like it.