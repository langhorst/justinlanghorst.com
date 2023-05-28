+++
title = "Using AirPort Utility 5.6.1 in Lion"
description = "How to use AirPort Utility to configure an AirPort Express within OS X Lion."
date = 2013-01-18T00:34:34Z
tags = ["development","utilities"]
+++

So I'm in the beginning stages of planning a whole-house automation system. Since music is pretty damn important to me, the first part of this system I'd like to get right is whole-house audio. I want to be able to play music in pretty much any room I'm in, all controlled from my phone or tablet. Since I'm already running Apple products everywhere, it just makes since to use an AirPort Express for each zone.

<!--more-->

To get things started, I have an older version of the AirPort Express that plugs directly into the wall. It hasn't been updated in a while, but my OS X systems have. I'm currently running Lion (not Mountain Lion) and the current AirPort Utility application (version 6.1) doesn't support the old AirPort Express. The older version that does support it only runs on Leopard/Snow Leopard. Or so it says. What to do?

Well, there is an [excellent explanation here](http://rants.srdxas.com/?p=249). I'll forgive the errors in the title of his/her site because the solution and walk-through are great.

Briefly, here's what you need to do:

  1. Download the disk image from [http://support.apple.com/kb/DL1536]().
  2. Mount the image / move `AirPortUtility.pkg` to your Desktop.
  3. Open **Terminal**, and `cd ~/Desktop ; mkdir tmp ; cd tmp`.
  4. Extract the payload: `xar -x -f ~/Desktop/AirPortUtility.pkg Payload`.
  5. Extract the app: `gzcat AirPortUtility.pkg/Payload | tar -xf -`.
  6. Open up the app located withing the new `Applications` directory.

Following the instructions above allowed me to finally fix my old AirPort Express to get it showing in my AirPlay list again. Now I just need to buy a few more to get audio in each room. I think it's time to go listen to some music. Yup.
