---
title: How to thoroughly destroy spam accounts from Mastodon
slug: how-to-thoroughly-destroy-spam-accounts-from-mastodon
publishDate: 2018-11-26T12:00:00.000Z
lastmod: 2019-11-26T06:10:02.000Z
tags: [Tech]
---

Well, it seems Mastodon have a problem with a particular spambot. As an instance admin, I investigated and here's my findings.

Symptoms: Accounts keep singing up on your instance. The username are all random syllables (kind of sounds like Japanese), they have random full English/American-sounding names, their email address are all from random domains as if their owner have a pretty big budget on that and they all have different IPs.

First step: Pass all of those IPs through the Whois database and find something in common. And indeed, they all seem to come from the same ISP, for the most part, so it's trivial to craft a command to get their IP ranges.

(Note: The next commands have been tested at the time of writing on a Debian 9 server, on other OSes, your mileage may vary. I say this because the BSD implementation of the whois tool is very different from its GNU counterpart, from what I can see. This also assumes that you're on the account Mastodon runs and `cd`'d in its live directory and you know that you shouldn't type the `$` as it's your prompt. And you actually know what you're doing, make backups first, etc. Also install the `ipcalc` package first, it'll help.)

    $ whois -i admin-c,tech-c GS19550-RIPE | grep inetnum | cut -f9- -d" " | sed "s/ //g" | xargs -n1 ipcalc -rn | grep -v deaggregate >> banlist.txt
    

(Actual command will vary from spambot to spambot, but it's the one the fediverse is currently plagued with and I have no doubt if there's another spammer it's going to be similar. Also from here, the instructions are for Mastodon 2.6, for any other software it's going to be something else, but yeah, you're basically going to kick IP ranges out.)

Now that you have a bunch of IPs to ban, enter the Rails console:

```sh
    $ RAILS_ENV=production bundle exec rails c
```

Now this bit of code will tell you their usernames, to make sure you have no false positives.

```ruby
    File.open("banlist.txt","r").each_line { |ip| User.where("last_sign_in_ip << ?", ip.strip).each { |u| puts u.account.username } };0
```

And now, this is the fun part, destroy them!

```ruby
    File.open("banlist.txt","r").each_line { |ip| User.where("last_sign_in_ip << ?", ip.strip).each { |u| u.account.user.destroy; u.account.destroy } };0
```

Now you can't see them anymore. If you missed a few, you can always put their IPs in `banlist.txt` and repeat the process. Now to make sure they're not going to come back, you can either put them in your iptables or your favourite firewall, or put a blacklist in your nginx. Run this as your normal admin user:

```sh
    $ cat banlist.txt | xargs -n1 -I'{}' echo deny '{}'\; | sudo tee /etc/nginx/blacklist
```

Then edit your nginx config and put an `include blacklist;` somewhere in your instance's server block. Don't forget to reload nginx!

And there you go. How to find out and destroy spambots for dummies. Have fun and don't forget to do this once in a while :)

Sources:
[https://mastodon.at/@pfigel/100076564629123760](https://mastodon.at/@pfigel/100076564629123760)
[https://github.com/tootsuite/mastodon/issues/109#issuecomment-298767655](https://github.com/tootsuite/mastodon/issues/109#issuecomment-298767655)
