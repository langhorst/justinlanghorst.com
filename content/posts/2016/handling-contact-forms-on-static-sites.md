+++
date = "2016-09-03T22:17:34-05:00"
draft = false
title = "Handling contact forms on static sites"
tags = ["development","static","jamstack"]
+++

I originally went the route of rolling my own contact form handler. But instead of setting up my own server (even if mostly already written the way I would want it, see [contact-form](https://github.com/jmoiron/contact-form/) on GitHub), daemonizing it, proxying requests from Nginx, and then remembering how I set it all up 6 months from now, I found another service.

[FormKeep](https://formkeep.com/) look awesome, but their lowest pricing option is way more expensive than I need for a paltry little contact form on a site that barely gets any traffic.

Continuing my search, I found [Formspree](https://formspree.io/). Free for 1,000 submissions a month. It handles my personal [contact form] perfectly. They also offer an upgraded membership option for $10/month and the whole thing is open source if you care to host your own copy.

And now I’m tempted to go write my own generic form handler written in Golang. Another day.


