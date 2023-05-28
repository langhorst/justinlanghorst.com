+++
title = "Octopress on S3"
description = "How to set-up Octopress, a static blogging engine, on the Amazon S3 service."
date = 2013-07-11T16:23:23Z
tags = ["development","octopress","amazon s3","writing"]
+++

I'm currently working on redoing a company website, moving from [WordPress](https://wordpress.org/) to [Nanoc](http://nanoc.ws/). Since the new website will be static, and mostly a single-pager for a while, we now have the option of moving from a traditional host to the [Amazon S3](https://aws.amazon.com/s3/) datastore. Before committing a company website to the process, I decided to go ahead and move this blog over to S3 (it uses [Octopress](http://octopress.org/)) first. Here are a few notes that I jotted down along the way.

<!--more-->

## Sign-up to S3 and create buckets

Amazon has excellent documentation for hosting a static website on S3. I basically followed [this excellent walkthrough](http://docs.aws.amazon.com/AmazonS3/latest/dev/website-hosting-custom-domain-walkthrough.html). To begin, create two buckets for your domain, one with the "www" subdomain and one without. Point the "www" bucket to the non-www bucket, and keep all your files there. Or just ignore the "www" version entirely if you don't care to resolve the www subdomain to your website. Some might say it's deprecated anyway.

### Add S3 deployment to Octopress

Jacob Elder wrote about deploying Octopress to S3 and also included a bit of code for deployments using `s3cmd`. Check out the [blog post](http://blog.jacobelder.com/2012/03/deploying-octopress-to-amazon-s3/) for detailed info, or just update your Rakefile with the contents of [this patch](https://github.com/imathis/octopress/pull/455/files).

### Updates to Jacob's patch

I ended up changing a few things from Jacob's code, mostly just adding the option to use [reduced redundancy](http://docs.aws.amazon.com/AmazonS3/latest/dev/Introduction.html#RRS) since the contents of my website are most definitely "durably stored elsewhere".

```
## -- S3 Deploy Config -- ##
# Requires s3cmd. `brew install s3cmd` or see http:/s3tools.org/download 
s3_bucket     = "justajot.com"
s3_delete     = false
s3_reduced    = true 
```

That's the config, and this is the Rake task:

```ruby
desc "Deploy website to Amazon S3"
task :s3 do
  puts "## Deploying website via s3cmd"
  exclude = File.exists?("./s3-exclude") ? "--exclude-from '#{File.expand_path('./s3-exclude')}'" : ""
  ok_failed system("s3cmd sync --guess-mime-type --acl-public #{exclude} #{'--delete-removed' unless s3_delete == false} #{'--reduced-redundancy' unless s3_reduced == false} #{public_dir}/ s3://#{s3_bucket}/") 
end   
```

### Create routes, switch DNS servers

Keep following Amazon's walkthrough. Once you've switched over your DNS servers to point to those listed under the Route 53 entry, all you have    to do is wait a bit to see if you did everything correctly. If you're reading this, I'm guessing everything my attempt was a success. ;-)


