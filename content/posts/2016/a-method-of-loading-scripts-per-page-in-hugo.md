+++
date = "2016-09-05T13:45:57-05:00"
title = "A method of loading scripts per page in Hugo"
description = "How to load scripts per page using the Hugo static site generator"
tags = ["hugo"]

+++

Sometimes you find the need to make a specific page interactive with a bit of custom Javascript. This post explains how to do just that using the Hugo static site generator. Instead of loading the script globally for every page on the site, load it for that page and that page alone.

<!--more-->

In the page's meta section, we define a `scripts` parameter. Note that we are using TOML for the meta block in our Markdown files:

```toml
scripts = ["a.script.file.js","another.script.file.js"]
```

Now we can address the template code that will be used to render this page. In my case, I have a partial designated to load script files. This partial is used in every template. Within the template we can include a single line to load all additional script files defined in the metablock of the Markdown file:

```go
{{ if isset .Params "scripts" }}{{ range .Params.scripts }}<script src="{{ printf "assets/js/%s" . | absURL }}"></script>{{ end }}{{ end }}
```

That's it! Now we can load additional scripts per-page instead of globally. This saves bandwidth, thereby keeping page-load times for the rest of the site down from unnecessary increases in page load size.
