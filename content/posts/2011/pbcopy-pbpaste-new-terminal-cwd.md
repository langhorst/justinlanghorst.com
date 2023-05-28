+++
title = "pbcopy | pbpaste, and opening a new terminal tab in the current working directory"
description = "Using the OS X command-line utilities pbcopy and pbpaste within the terminal."
date = 2011-10-31T15:59:59Z
tags = ["development","utilities","tools"]
+++

While I try my best to improve all areas of my life continuously ([kaizen](http://en.wikipedia.org/wiki/Kaizen)), sometimes I fail to perform with exemplary status. Countless times a day I have the need to open a new Terminal tab in the same working directory. Before today, I issued a pwd command, copied the output using the mouse, `Command-T`, and `cd Command-V`. Ugh. That’s a convoluted mess.

<!--more-->

When I was growing up and mastering conventional memory management in DOS for the sole purpose of viewing various graphical demos from around the world, I knew about and how to use every command available within the operating system. Even though that’s probably not realistic these days, it’s something definitely worth shooting for over time (hmmm [Linux from Scratch](http://www.linuxfromscratch.org/). If the same were true today, then I would have already been using the [`pbcopy`](http://developer.apple.com/library/mac/#documentation/Darwin/Reference/ManPages/man1/pbcopy.1.html) and [`pbpaste`](http://developer.apple.com/library/mac/#documentation/Darwin/Reference/ManPages/man1/pbpaste.1.html) utilities.

Now I can simply pipe the current working directory to the clipboard:

    pwd | pbcopy

Open a new terminal tab or window, and use a combination of cd and Command-V or pbpaste to get back to the original directory:

    cd `pbpaste`
    cd [Command-V]

While `pbcopy` and `pbpaste` are excellent utilities, they aren’t the best solution for what I originally set out to do: open a new tab in the current working directory. I’m sure there is an even quicker way to get to the same current working directory, probably with an AppleScript, and I suspect that OS X Lion has an option for this in Terminal, but for now, `pbcopy` will do the trick.
