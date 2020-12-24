---
title: "How to run Jekyll locally with Docker and Docker Compose"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - Jekyll
  - Docker
---

As I have some free time with the holidays, I decided to do a revamp on my website. After exploring all the new hyped static site generators that are trending now, I decided to continue using Jekyll and GitHub Pages.

As I am totally inexperienced with Ruby, installing the Jekyll environment setup on my machine was always an issue for me, so I decided to just use Docker this time (after struggling for an hour trying to install it locally on my mac). I will quickly describe how I did it.

<!--more-->

## Docker Compose setup

My plan is to be able to do a *docker-compose up* to be to run an instance of *jekyll serve* on my localhost, with all the auto rebuilding and live reloading goodies.

Jekyll has an official docker image, so I created a simple *docker-compose.yml* file that creates a container with the official image, a volume that maps to the root of my repo and made it execute *jekyll serve.*

```yaml
services:
  jekyll:
    image: jekyll/jekyll:latest
    command: jekyll serve --watch --force_polling --verbose --livereload
    ports:
      - 4000:4000
    volumes:
      - .:/srv/jekyll
```

With this compose file defined, *docker-compose up* will then serve my website on *[localhost:4000](http://localhost:4000)* 

Way easier than I expected 🙂