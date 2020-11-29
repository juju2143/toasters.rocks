---
title: Esoteric uses of CGI
slug: esoteric-uses-of-cgi
publishDate: 2019-12-21T04:41:19.000Z
lastmod: 2019-12-21T05:34:28.000Z
tags: [Tech]
description: Or how to program the back-end of your website using Commodore BASIC.
images:
- /images/2019/12/photo-1461749280684-dccba630e2f6.jpg
---

Well, you probably all heard of [esoteric programming languages](https://esolangs.org/) before, but the question today is, programming languages used outside its intended use, would that be esoteric?

If I tell you back-end web languages, you'd immediately think PHP, Node.js, Ruby, C maybe, but what if I tell you... Commodore BASIC? Sure, just get a Commodore 64 with a TCP/IP stack and write a web server for it you'd say, but I mean, actually using it on an actual, everyday web server on some Linux box? It's clearly not made for that.

Enter [CGI](https://en.wikipedia.org/wiki/Common_Gateway_Interface), which is a protocol that was basically made so your web server can run a Perl script that could do more than directly serving some static HTML file so your user could actually interact with your website. A standard setup would be PHP running as a FastCGI server on top of Nginx, that or running as a plugin on Apache. In the first case, PHP communicates with Nginx via a UNIX socket, the web server forwards a request, PHP processes it and sends it back, so it would be more efficient and not spawn a process every request, which might slow your site down if it's popular enough.

## Installation

We're going to use [fcgiwrap](https://github.com/gnosek/fcgiwrap), a little piece of software that plugs into Nginx just like PHP does, but it runs just about any software you can run on command line, be it a shell script, a program, or a script written using your favourite language interpreter. You just install it using your Linux distro's package manager, activate the socket with a `sudo systemctl start fcgiwrap.socket`, install it in your `nginx.conf` just like you did with PHP, that's it, just like in the good ol' days.

```nginx
location ~* \.cgi {
    root           /srv/http;
    fastcgi_pass   unix:/run/fcgiwrap.sock;
    fastcgi_index  index.cgi;
    include        fastcgi.conf;
}
```
Example nginx config block.

So, as long as you have an interpreter, you can turn it into a web back-end server language, right? Theorically, yes. We'll take our good ol' Commodore 64 BASIC [that has been ported to C](https://github.com/mist64/cbmbasic) so it could work on your modern computer as a case study.

So basically, you need a console program that opens a file and interprets it:

```bash
$ cat program.bas
#!/usr/bin/cbmbasic
10 PRINT"Hello, world!"
$ chmod +x program.bas
$ ./program.bas
Hello, world!
```
Example console session.

Mind the shebang (the `#!/usr/bin/cbmbasic` line), it basically turns `./program.bas` into the proper `cbmbasic program.bas`. You'll need to run `chmod +x` on it for it to work. It's going to be useful later as you have no way otherwise to tell your web server which interpreter you want to run that file with.

So now you can dump in your web directory a file like this:

```basic
#!/usr/bin/cbmbasic
10 PRINT"HTTP/1.1 200 OK
20 PRINT"Content-Type: text/html;charset=utf-8
30 PRINT
40 PRINT"<html><head><title>Hello</title></head><body>
50 PRINT"Hello, world!
60 PRINT"<br/>Time is: ";
70 PRINT TI$
1000 PRINT"</body></html>
```
index.cgi

Navigate to it with your browser and sure enough, you have a nice "Hello, world!" with the current time. So it's really easy to code something to show up in your browser. But of course, this ain't PHP, you have to send the HTTP headers yourself (lines 10-30, anything followed by two newlines should be sufficient, but for best results you should send the HTTP code and the content type), but still, quite easy. But the question here is, how is it useful?

## $_POST

Obviously, you'd need some kind of input, right? The HTTP protocol allows for GET and POST. POST allows you to send data in the body of the request, otherwise you can also get some data from the URL. In PHP that would be respectively the `$_POST` and `$_GET` arrays. But of course it won't automatically parse these, so let's do that.

For POST data, it's just as easy as reading keyboard input, or STDIN, depending of how it works. If it's a GET request, then you should get nothing (or a 0xFF byte in case of BASIC), otherwise you'd get the data your user sent in your POST form.

```basic
100 PRINT"<form method=POST action=''>
110 PRINT"<input name='input'></input>
120 PRINT"<input type=hidden name='hidden' value='value'></input>
130 PRINT"<button type=submit>Submit</button>
140 PRINT"</form><pre>

200 B$=""
210 GET A$
220 IF A$="&" GOTO 260
230 IF ASC(A$)>127 GOTO 260
240 B$=B$+A$
250 GOTO 210
260 PRINT B$
270 IF ASC(A$)<128 GOTO 200
280 PRINT"</pre>
```

Add this code to get and print POST variables. Good thing there's line numbers so I don't have to tell you where to add them.
It's safe to assume anything over 127 is the end of input, as anything above that will be percent-encoded. In particular, in BASIC, you get 199 if you're past the end (EOF) and 255 if there's nothing. Parsing the resulting string is left as an exercise to the reader. And now if you send some data in the form you'll get:

```
    input=Something
    hidden=value
```

You can even use brainfuck or something similar:

    100 PRINT"<form method=POST action='bf.cgi'>

Replace line 100 from above...
```bf
#!/usr/bin/bfi
-[------->+<]>-.>-[--->+<]>-..----.[->+++++++<]>-.++.---.+++.-[--->++<]>.[-->+++<]>++.--..[--->++<]>.[-->+++++<]>-.----.>++++++++++..,+[-.,+]
```
...and create bf.cgi
## $_SERVER, $_GET, $_COOKIES, etc.

Now you'll want some of the sweet variables the server sends you that tells where the request came from and similar stuff, which is `$_SERVER` in PHP. If your language doesn't support environment variables from the OS, unfortunately (for brainfuck), you'll need to open files here. On Linux, it's `/proc/self/environ`, your mileage may vary on other OSes. It's mostly the same code, except the lines are separated by null bytes.

```basic
300 OPEN 1, 1, 0, "/proc/self/environ"
310 B$=""
320 GET#1, A$
330 IF A$="" GOTO 370
340 IF ASC(A$)>127 GOTO 370
350 B$=B$+A$
360 GOTO 320
370 PRINT B$
380 IF A$="" GOTO 210
390 IF ASC(A$)<128 GOTO 310
395 CLOSE 1
```
You know what to do at this point.

Again, parsing the results is left as an exercise to the reader. Notice that Commodore BASIC treats null bytes as an empty string here, which completely breaks the `ASC()` function and so should be handled separately.

Interesting variables are `QUERY_STRING` (PHP's `$_GET`, parses just like we did with POST), `HTTP_COOKIE` (cookies are here), `REQUEST_METHOD` (GET or POST), `REMOTE_ADDR` (your user's IP), `REQUEST_SCHEME` (https?), you can find a lot of them [just here](https://www.php.net/manual/reserved.variables.server.php) (at least those who aren't specific to PHP) or [here](https://www.rfc-editor.org/rfc/rfc3875.html#section-4.1).

On that, that's it for today, hope you have fun with this! I wonder what kind of weird stuff you'd make with this, please tell me if you code your website in an esoteric language :)
