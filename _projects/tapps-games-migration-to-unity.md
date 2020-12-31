---
title: "Tapps Games Migration to Unity"
date: "2019-08-01"
classes: wide
excerpt: "Migration of Tapps Games technical stack for game development to Unity."
header:
  teaser: /assets/images/tapps/logo.png
sidebar:
  - title: "Role"
    image: /assets/images/tapps/logo.png
    image_alt: "logo"
    text: "Senior Software Engineer"
  - title: "Company"
    text: "Tapps Games"
  - title: "Responsibilities"
    text: "Technical Leadership. Software Archicture. Build automation."
---

After working on Tapps' two first mobile games made with Unity, I began to work on an ambitious project: do all preparations to pivot the development of all new games from a proprietary game engine to Unity.

I worked on this project for one year as a senior engineer and one of the technical leaders. To pursue the goal of a smooth transition to Unity, my team was responsible for multiple different tasks: providing training for production teams, research solutions for existing issues with our development process, map and fix existing issues with our existing shared libraries and continuous integration infrastructure.

## Main contributions
- Began to restructure our shared libraries from a single monolithic library to multiple small libraries
- Introduced Unity Package Manager and a private NPM registry as the official way to release and distribute shared libraries
- Interview all production teams (game designers, artists, programmers, etc.) to map existing issues and improve development processes
- Refactored our internal build system, removed shared dependencies with our internal game engine build process
- Reduced build time up to 60% by using [Unity Cache Server](https://github.com/Unity-Technologies/unity-cache-server)
- Made a Prof of Concept of creating build agents using Docker and Unity images. Enabling easier scaling and Unity updates
- Introduced [Unity Jar Resolver](https://github.com/googlesamples/unity-jar-resolver) to handle native plugin dependencies resolution, reducing friction and enable more frequent updates
