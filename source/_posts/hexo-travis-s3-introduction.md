title: 'Hexo, Travis, S3 - Introduction'
tags:
  - Hexo
  - Travis CI
  - AWS
  - Amazon S3
date: 2015-07-29 20:35:34
---


This series will guide you through all the steps needed from setting up your Hexo website to continuously deploying it on Amazon S3 using Travis CI.

<!-- more -->

## Content

It covers:

* Introduction
* Part #1: Setting up Hexo
* Part #2: The Amazon S3 bucket
* Part #3: Deploying via Travis CI
* Part #4: Speed Improvements

## The Stack

### Hexo

You might have heard of static site generators such as Jekyll or Middleman.
After fiddling around with some of them, I finally settled with [Hexo](https://hexo.io/) which is build on Node.js.

I'm a ~~JavaScript~~ CoffeeScript developer, so Node.js was the platform of my choice when I was looking for a blogging platform. It allows you to write your own plug-ins as nothing else but Node.js modules and Hexo themes allow you to use template engines like Jade, Handlebars, EJS, etc.

### Travis CI

If you haven't heard of [Travis CI](https://travis-ci.org/) or Jenkins, you maybe never heard of continuous integration. Travis CI is a cloud based service which detects commits and pull requests on your GitHub based repository and performs predefined actions like building, testing and deploying your repository.

And guess what: It's totally free for your open source projects!

### Amazon S3

[Amazon S3](https://aws.amazon.com/s3/) is a storage service for hosting static files. Since we use a static site generator for our website and don't need any expensive computing power but only a service to serve our files, this is the AWS service we're looking for.

## Conclusion

Though, there are some downsides of using a static site generator over a CMS for blogging (e.g. there is no easy way to schedule posts) there are a lot of benefit: 

* Speed: All your files are pre-rendered and only wait to be served.
* Cost: Since your files are pre-rendered, there is no expensive computing power needed to process requests.
* Security: There is no admin interface to hack - which is a pita on WordPress.

In the first part of this series, we're going to set up our first Hexo website, get to know the basics and install a theme.
