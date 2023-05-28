+++
title = "Wiki on BeagleBone Black"
description = "How to set-up a wiki for home or business use on the BeagleBone Black computer."
date = 2015-01-03T18:00:00Z
tags = ["development","wiki","goiki","beaglebone","debian"]
+++

I just picked up a Rev 4 [BeagleBone Black][] from [Micro Center][] for ~$40 with the intent of using it as a wiki server for both my own personal notes and those for my home and family. While the BeagleBone comes with an embedded 4GB chip pre-installed with Debian, I need more space to store my notes, so I also picked up a 16GB micro SD card.

Since the BeagleBone Black (BBB) is more powerful than the Raspberry Pi, I suppose you could use pretty much any wiki software that runs on the ARM architecture with reasonable performance. But, I wrote software specifically for this purpose: a Git-backed Markdown-based wiki in a single executable - [Goiki][]. This is what I'll be running.

<!--more-->

First, I connected the BBB to my network using the supplied RJ45 jack on the board. To power it up, I used the supplied USB cable and connected it to an open USB port on my laptop.

To find the IP address, I logged-in to my router to see what changed on my DHCP server. Ah, there it is! It shows up with a hostname of "beaglebone". I'll just use that.

    ssh root@beaglebone

There is no password associated with the `root` account. This will need to change. I used `passwd` to update the password.

I like to tackle hardware issues first so I then set-up the microSD card to act as storage for my notes. Debian is set-up to auto-mount the micro SD card, so I was able to use `df -h` to see what loaded.

The filesystem that was set-up on the card wasn't appropriate for my needs (well, I suppose it was technically, but I wanted to change it anyway). So, I used `fdisk` to remove the current partitions and re-created one big partition. I then used `mkfs.ext4` to make an ext4 filesystem on that partition. Then it was ready to go.

To make sure the partition is always available, a mount-point for the device needed to be set-up. The partition is located at `/dev/mmcblk1p1` and I mounted it at `/media/microsd` (`mkdir /media/microsd` first). Below is the entry I used in `/etc/fstab` to set-up the mount point:

    /dev/mmcblk1p1	    /media/microsd	ext4	defaults	0	2

Now the storage device automatically mounts upon boot-up to `/media/microsd`. My Goiki data directories were then set-up on the device.

    mkdir -p /media/microsd/goikipersonal/data
    mkdir -p /media/microsd/goikifamily/data
    
---

#### Install Goiki

Goiki is a Git-based Markdown-powered wiki written in Golang. I wrote it specifically for the purpose of having a simple wiki for low-powered ARM devices such as the Raspberry Pi and BeagleBone Black. Of course it can also run on many other platforms too.

I don't want to compile the project on the ARM device itself due to unnecessary thrashing of the micro SD card. Instead, I cross-compile the binary from my Mac. Maybe one day I'll have binaries available for multiple platforms, but since Goiki is in such a beta state (though completely usable), you'll have to compile it yourself.

#### A quick primer on cross-compiling Go for ARM on OS X

Read the [Go: Getting Started][] section on the Golang website first. Though, the following might get you there without needing to do that:

    brew install go
    mkdir ~/go
    export GOPATH=~/go
    cd ~/go
    mkdir -p src/github.com/langhorst
    cd src/github.com/langhorst
    git clone https://github.com/langhorst/goiki.git
    cd goiki
    go get
    
For cross compiling Go projects on OS X for ARM, do the following once:

    cd `brew --prefix go`
    cd libexec/src
    GOOS=linux GOARCH=arm CGO_ENABLED=0 ./make.bash --no-clean
    
And then to compile Goiki:

    cd ~/go/src/github.com/langhorst/goiki
    GOOS=linux GOARCH=arm GOARM=7 go build -o goiki.arm
    
Now copy over the binary:

    scp root@beaglebone:goiki.arm
    
And on the BBB:

    mv ~/goiki.arm /usr/local/bin/goiki
    
Now we can run the `goiki` executable.

#### Configure Goiki

Goiki packages its default configuration in the binary and provides a command line option for outputting it to the command line. Capture the output to a configuration file:

    `mkdir /etc/goiki`
    `goiki -d > /etc/goiki/personal.toml`
    `goiki -d > /etc/goiki/family.toml`
    
The configuration file is self-documenting. Edit with your favorite editor and come back for setting up a daemon.

#### Setting up a daemon

Since `init` is available we'll just use that. I found a [terrific init.d script template](http://werxltd.com/wp/2012/01/05/simple-init-d-script-template/) to make this easy. You'll just need to change the first couple of lines for your own executable:

``` bash
#!/bin/bash
# myapp daemon
# chkconfig: 345 20 80
# description: myapp daemon
# processname: myapp

DAEMON_PATH="/home/wes/Development/projects/myapp"

DAEMON=myapp
DAEMONOPTS="-my opts"

NAME=myapp
DESC="My daemon description"
PIDFILE=/var/run/$NAME.pid
SCRIPTNAME=/etc/init.d/$NAME

case "$1" in
start)
	printf "%-50s" "Starting $NAME..."
	cd $DAEMON_PATH
	PID=`$DAEMON $DAEMONOPTS > /dev/null 2>&1 & echo $!`
	#echo "Saving PID" $PID " to " $PIDFILE
        if [ -z $PID ]; then
            printf "%s\n" "Fail"
        else
            echo $PID > $PIDFILE
            printf "%s\n" "Ok"
        fi
;;
status)
        printf "%-50s" "Checking $NAME..."
        if [ -f $PIDFILE ]; then
            PID=`cat $PIDFILE`
            if [ -z "`ps axf | grep ${PID} | grep -v grep`" ]; then
                printf "%s\n" "Process dead but pidfile exists"
            else
                echo "Running"
            fi
        else
            printf "%s\n" "Service not running"
        fi
;;
stop)
        printf "%-50s" "Stopping $NAME"
            PID=`cat $PIDFILE`
            cd $DAEMON_PATH
        if [ -f $PIDFILE ]; then
            kill -HUP $PID
            printf "%s\n" "Ok"
            rm -f $PIDFILE
        else
            printf "%s\n" "pidfile not found"
        fi
;;

restart)
  	$0 stop
  	$0 start
;;

*)
        echo "Usage: $0 {status|start|stop|restart}"
        exit 1
esac
```

I set one up for each of my wikis: `goikipersonal` and `goikifamily`.

    chmod +x /etc/init.d/goikipersonal
    chmod +x /etc/init.d/goikifamily
    
An example of how I changed the `goikifamily` init script:

``` bash
#!/bin/bash
# goiki daemon
# chkconfig: 345 20 80
# description: goiki family daemon
# processname: goiki

DAEMON_PATH="/usr/local/bin"

DAEMON=goiki
DAEMONOPTS="-c=/etc/goiki/family.toml"

NAME=goiki
DESC="Goiki wiki for family notes"
```
    
Now, start them up!

    /etc/init.d/goikipersonal start
    /etc/init.d/goikifamily start
    
You should now be able to hit the box from your favorite webbrowser: `beaglebone:[port]`.

[BeagleBone Black]: http://beagleboard.org/black
[Micro Center]: http://www.microcenter.com/
[Goiki]: http://github.com/langhorst/goiki
[Go: Getting Started]: http://golang.org/doc/install