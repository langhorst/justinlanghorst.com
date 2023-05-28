---
title: "A Big Smile ... Ruby and Erlang"
date: 2006-08-25T12:34:11-06:00
tags:
  - programming
  - ruby
  - erlang
#summary: ""
showToc: true
TocOpen: false
hidemeta: false
comments: false
#description: "Desc Text."
#canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
#cover:
#    image: "<image path/url>" # image path/url
#    alt: "<alt text>" # alt text
#    caption: "<text>" # display caption under cover
#    relative: false # when using page bundles set this to true
#    hidden: true # only hide on current single page
#editPost:
#    URL: "https://github.com/<path_to_repo>/content"
#    Text: "Suggest Changes" # edit text
#    appendFilePath: true # to append file path to Edit link

---

A big smile appeared on my face tonight after typing the following:

```erlang
-module(math1).
-export([factorial/1]).

factorial(0) -> 1;
factorial(N) -> N * factorial(N-1).
```

```ruby  
def factorial(n)
  return 1 if n == 0
  n * factorial(n-1)
end
```

Maybe not so obvious. But check this out:

```erlang
-module(temp).
-export([convert/2]).

convert({fahrenheit, Temp}, celsius) ->
        {celsius, 5 * (Temp -32) / 9};
convert({celsius, Temp}, fahrenheit) ->
        {fahrenheit, 32 + Temp * 9 / 5};
convert({reaumur, Temp}, celsius) ->
        {celsius, 10 * Temp / 8};
convert({celsius, Temp}, reaumur) ->
        {reaumur, 8 * Temp / 10};
convert({X, _}, Y) ->
        {cannot,convert,X,to,Y}.
```

This a simple temperature conversion program. Done in Ruby with methods, although not normally the way that I would introduce such functionality into a program:

```ruby
def convert(from, temp, to)
  case to
    when :celsius     : to_celsius(from, temp)
    when :fahrenheit  : to_fahrenheit(from, temp)
    when :reaumur     : to_reaumur(from, temp)
    else                "cannot convert #{from} to #{to}"
  end
end

def to_celsius(from, temp)
  case from
    when :fahrenheit  : 5.0 * (temp - 32.0) / 9.0
    when :reaumur     : 10.0 * temp / 8.0
    when :celsius     : temp
    else                "cannot convert #{from} to celsius"
  end
end

def to_fahrenheit(from, temp)
  case from
    when :celsius     : 32.0 + temp * 9.0 / 5.0
    when :fahrenheit  : temp
    else                "cannot convert #{from} to fahrenheit"
  end
end

def to_reaumur(from, temp)
  case from
    when :celsius     : 8.0 * temp / 10.0
    when :reaumur     : temp
    else                "cannot convert #{from} to reamur"
  end
end
```

Yikes! The pattern matching in Erlang functions really comes in handy. Here is the output from both terminals (Erlang then Ruby):

```erl
justinmbp:~ justin$ erl
Erlang (BEAM) emulator version 5.5 [source] [async-threads:0]

Eshell V5.5  (abort with ^G)
1> c(temp).
{ok,temp}
2> temp:convert({fahrenheit, 98.6}, celsius).
{celsius,37.0000}
3> temp:convert({reaumur, 80}, celsius).
{celsius,100.000}
4> temp:convert({reaumur, 80}, fahrenheit).
{cannot,convert,reaumur,to,fahrenheit}
5> temp:convert({celsius, 37}, fahrenheit).
{fahrenheit,98.6000}
6> temp:convert({celsius, 100}, reaumur).
{reaumur,80.0000}
7>
```

```irb
justinmbp:~ justin$ irb
irb(main):001:0> require 'temp'
=> true
irb(main):002:0> convert(:fahrenheit, 98.6, :celsius)
=> 37.0
irb(main):003:0> convert(:reaumur, 80, :celsius)
=> 100.0
irb(main):004:0> convert(:reaumur, 80, :fahrenheit)
=> "cannot convert reaumur to fahrenheit"
irb(main):005:0> convert(:celsius, 37, :fahrenheit)
=> 98.6
irb(main):006:0> convert(:celsius, 100, :reaumur)
=> 80.0
irb(main):007:0>
```

And I'm still leaving out the fact that Erlang is returning a Tuple that includes what type of data to work with. With Ruby, it's a simple Fixnum. I suppose I could have used a Hash to store that information in the return value, but ... this was suppose to be a simple little example that turned into something a little larger than I expected.

For a more fair look at the Ruby code, here is how I would have normally structured temperature conversion functionality:


```ruby
module Temp
  class Fahrenheit
    attr_accessor :temp

    def initialize(temp=0)
      @temp = temp
    end

    def to_celsius
      5.0 * (@temp - 32.0) / 9.0
    end
  end

  class Celsius
    attr_accessor :temp

    def initialize(temp=0)
      @temp = temp
    end

    def to_fahrenheit
      32.0 + @temp * 9.0 / 5.0
    end

    def to_reaumur
      8.0 * @temp / 10.0
    end
  end

  class Reaumur
    attr_accessor :temp

    def initialize(temp=0)
      @temp = temp
    end

    def to_celsius
      10.0 * @temp / 8.0
    end
  end
end
```

While it's not really the same and I am comparing apples to oranges, I still find it very interesting. This is my first foray into a functional language. I'm currently reading Concurrent Programming in Erlang and hope to be in to some of the application code by the end of the weekend.

After taking some good notes, hopefully I'll be able to re-create in Erlang a light message queuing/routing system I wrote in Ruby this week.
