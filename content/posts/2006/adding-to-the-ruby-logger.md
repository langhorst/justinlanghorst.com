---
title: "Adding to the Ruby Logger"
date: 2006-06-23T12:34:11-06:00
tags:
  - programming
  - ruby
  - development
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

I've been working on some new code at work for a new install. Unfortunately, I'm unable to use the new framework just yet (mainly because it's not complete), and I needed a workaround for the Ruby Logger to be able to accept `.trace` calls (we usually use [Log4r](http://log4r.sourceforge.net/). Here's my workaround:

```ruby
require 'logger'

# Redefine the Logger to allow for TRACE calls.
class Logger
  module Severity
    TRACE = -1
  end
  
  def trace?; @level <= TRACE; end
  
  def trace(progname = nil, &block)
    add(TRACE, nil, progname, &block)
  end
  
  NEW_SEV_LABEL = %w(TRACE DEBUG INFO WARN ERROR FATAL ANY)
  
  def format_severity(severity)
    NEW_SEV_LABEL[severity+1] || 'ANY'
  end
end
```

Usage example:

```ruby
log = Logger.new
log.level = Logger::TRACE
log.trace 'TRACE test message.'
```

```
> T, [2006-06-22T20:07:56.244922 #3053] TRACE -- : TRACE test message.
```
