---
title: "My Arch Linux packages: how to create your own repo"
slug: arch-linux-packages
publishDate: 2020-02-22T07:41:21.000Z
lastmod: 2020-02-22T21:49:44.000Z
tags: [Tech]
---

As I use Arch Linux as my daily driver on my main machine now and I use a lot the AUR nowadays (most notably I maintain the packages for The 8-Bit Guy's Commander X16), figured I might create my own repository, right?

So there you go, after figuring it out on the Arch Wiki, here's a collection of software I maintain on the AUR, some of my own things I bothered to make a PKGBUILD for, or just software not found in other binary repos I know the users of my communities will like. So, as I write this, mostly Commander X16 stuff and TI-83 stuff.

    [juju]
    SigLevel = Never
    Server = https://repo.juju2143.ca/archlinux/$repo/os/$arch

Add this to your pacman.conf
Then, you can see every package I added with `pacman -Sl juju`.

But why? Here's the gory details. A friend of mine told me about this service called [Netlify](https://www.netlify.com/), which is pretty much for those who made their website with a site generator, it automatically builds the website each time you push it to Git. From what I understand, anyway. Kinda like GitHub, I guess. The free plan comes with custom domains, HTTPS, 300 build minutes a month, 100 GB of bandwidth and... no storage limit? Well, that'd probably make for a good use case for this project.

So I follow [the instructions on the wiki](https://wiki.archlinux.org/index.php/Pacman/Tips_and_tricks#Custom_local_repository), I throw in some index.html generated from stuff I stole from the x16-emulator package, I do a manual deploy and there you go!

    repo-add archlinux/juju/os/x86_64/juju.db.tar.gz archlinux/juju/os/x86_64/some-package.pkg.tar.xz
    pandoc --from gfm --to html -c github-pandoc.css --standalone --metadata pagetitle="repo.juju2143.ca" README.md --output index.html
    tree archlinux -P "*.pkg.tar.xz" -v -H "https://repo.juju2143.ca/archlinux" > archlinux/index.html
    netlify deploy --prod --dir=.

To do everytime I update something
Symlinks don't work for some reason, so you'd need to add a file called `_redirects`:

    /archlinux/:repo/os/:arch/:repo.db /archlinux/:repo/os/:arch/:repo.db.tar.gz
    /archlinux/:repo/os/:arch/:repo.files /archlinux/:repo/os/:arch/:repo.files.tar.gz
    /* /404.html 404

Mind the very long lines
So there you go. It's very much in line with the principle that drives Arch Linux, the famous "keep it simple, stupid" (KISS), so that was pretty easy. So if you want a package I should add and/or build, feel free to suggest away :)

[Check out the repo here!](https://repo.juju2143.ca)
