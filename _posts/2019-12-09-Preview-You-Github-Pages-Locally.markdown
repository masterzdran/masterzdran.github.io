---
layout: post
title: Previewing Your GitHub Pages locally
date: 2019-12-08 18:00:00 +0100
description: Previewing your post is very important, allowing you to save a lot of time. 
img: github-pages.jpg
cover: github-pages-cover.jpg
tags: [Jekyll, GitHub, Markdown, GitHub Pages, Docker, Docker Compose]
---

Recently I decided to try it out the [Github Pages](https://pages.github.com/) and start blogging with simple markdown. 
> Why?! Because I'm lazy. :) 

Since I write most of my documentation and presentations in markdown (this is a topic for another post), so why not my posts.

I wanna to most something less demanding than html and more dynamic that pure static content, so in the [official documentation](https://pages.github.com/) they refer:
> Using Jekyll, you can blog using beautiful Markdown syntax, and without having to deal with any databases.

That is perfect!

I will not explain how to configure your github account to do so, the [Github Pages documentation](https://pages.github.com/) is pretty good, even I could configure it without issues. :) 


# Jekill - Let us start
Once Configure let us do some [Jekyll](https://jekyllrb.com/). 

Did I mention that I'm lazy? Yeah, after some reading I notice that I had a "long way" until I had my **github page** online. So I went online, searched "Jekyll themes", enter [Jekyll Themes](http://jekyllthemes.org/) and after some browsing I had chosen [Flexible Jekyll](http://jekyllthemes.org/themes/flexible-jekyll/). Btw, Thank You @artemsheludko.

After some configuring and writing, site was up and running. Not before having to commit/push a couple of times because the output was "wrong".

# Jekill - Let us start ... Again
I wrote my [last post](https://masterzdran.github.io/Blazing-Blazor/) in markdown, using [Visual Studio Code](https://code.visualstudio.com/) and with the greatest assistance of the Markdown Preview, I was able to write the post (almost) as I like it to be.

When I finish, commit/push and all hell brake loose. The images weren't showing, somo pieces of codes weren't shown. Guys (and Gals) it was over a dozen commits until I had the final output. That is, in lack of better name, just plain stupid.

So I went to the Jekyll on Github Pages [official documentation](https://help.github.com/en/github/working-with-github-pages) (by now you may notice that I go to the official documentation a lot...) and found an article in ["Testing your GitHub Pages site locally with Jekyll"](https://help.github.com/en/github/working-with-github-pages/testing-your-github-pages-site-locally-with-jekyll) wich was very promissing... until i read the pre-requirements:
> ## Prerequisites
> Before you can use Jekyll to test a site, you must:
> 
> * Install Jekyll.

Yeah, no thanks. I don't want to pollute my machine with more software that I may use or not quite often.

# Jekill - Let us start ... Third Attempt
Installing software is out of question. However, if there is a docker image out there that do the trick... Let me check.

So in Docker Hub there is an [official image](https://hub.docker.com/r/jekyll/jekyll) and the documentation redirects to the github... and lots of talk and configurations and so.

So, I went to google and search for "test jekyll locally docker" and reach James Sturtevant (@jsturtevant, btw thank you) [old post] (https://www.jamessturtevant.com/posts/Running-Jekyll-in-Windows-using-Docker/) and I liked what I saw. Simple and direct.

Going back to Docker Hub and found "starefossen/github-pages" image and it was recently updated (Yeah, a live project :)) and it was pretty well documented.

Now let me create a docker-compose.yml for my Github Pages, so I can test it locally, following the instructions (pretty much as documented):

```yaml
#------------------------------------------------------------------------------
# Jekyll serves Github Pages locally
# ... because I'm a developer and I too lazy.
#------------------------------------------------------------------------------
version: "3.0"
services:
    Jekyll:
        image: starefossen/github-pages
        container_name: JekyllServer
        ports:
            - 4000:4000
        volumes:
            - .:/usr/src/app
```

executing

```
docker-compose up -d
```

It does what I expected from it, from the root of the repository map the site at "/usr/src/app" within the container.

Now I can test the pages before commit/push the post the fixes after.

# Conclusion
From my little experience, Jekyll is pretty cool for serving markdown files as website pages. Docker, again, is great for allowing developing "stuff" without having to install anything on my machine (and works everywhere). 

Github pages, are awesome, allowing to have a great site without having to worry much with HTML, CSS and Javascript. It only serves static content, but I don't mind... for now.

# Resources
* [2019-12-09] https://pages.github.com/
* [2019-12-09] https://jekyllrb.com/
* [2019-12-09] https://github.com/artemsheludko/flexible-jekyll
* [2019-12-09] https://www.jamessturtevant.com/posts/Running-Jekyll-in-Windows-using-Docker/
