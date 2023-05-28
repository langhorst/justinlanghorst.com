+++
date = "2015-04-06T11:15:31-05:00"
draft = false
title = "Creating and rotating log files from piped input"
description = "On using the rotatelogs tool to rotate files based on time and/or size"
tags = ["development","utilities","logging","golang"]
+++

Say you have an application that sends logs to STDOUT and you want to capture that stream, log it to a file and rotate the files based on time and/or size. [rotatelogs](https://httpd.apache.org/docs/current/programs/rotatelogs.html) is a small utility bundled with Apache's HTTPD that can do it for you.

<!--more-->

I wrote a small sample program as a test, one that outputs a bunch of Lorem ipsum text to STDOUT (`lorem`):

```go
package main

import (
	"fmt"
	"math/rand"
	"time"

	//
	"github.com/drhodes/golorem"
)

func main() {
	for {
		fmt.Println(lorem.Paragraph(3, 8))
		time.Sleep(time.Duration(rand.Intn(1000)) * time.Millisecond)
	}
}
```

This prints a Lorem ipsum paragraph between 3 and 8 sentences once every (pseudo) random 0 to 1000 milliseconds.

Once built, you can do this:

    $ lorem | rotatelogs -l "lorem-%Y%m%d-%H%M%S.log" 86400 5M

This sends the output of `lorem` to a date/time based log file like `lorem-20150406-120400.log` and rotates the log every night at midnight and/or after the content reaches 5 megabytes in size.
    
The rotatelogs manfile includes other useful examples I recommend reading through.
