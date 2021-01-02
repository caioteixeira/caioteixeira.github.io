---
title: "Toy Engine"
date: "2017-05-01"
classes: wide
excerpt: "My toy D3D12 graphics engine"
header:
  teaser: /assets/images/toy-engine/Sponza.jpg
sidebar:
  - title: "Role"
    image: /assets/images/toy-engine/Sponza.jpg
    image_alt: "Sponza scene"
    text: "Sole Developer (Personal Project)"
  - text: "[<i class='fab fa-github'></i> Github Repository](https://github.com/caioteixeira/ToyEngine){: .btn .btn--primary}"
---

My toy game/graphics engine project, originally developed as my graduation project and then became my favorite pet project, 
my main goal with this project was to have to a good testbed for my engine and graphics programming experiments and learn more about D3D12 and graphics programming.

## Current Features
* Direct3D 12 Multithreaded Renderer
* Phong Lighting (Point and ambient lights only)
* OBJ meshes and MTL materials loading, using [TinyObjLoader](https://github.com/syoyo/tinyobjloader)
* Entity Component System architecture, based on [EntityX](https://github.com/alecthomas/entityx)
* Integrated profiler, using [EasyProfiler](https://github.com/yse/easy_profiler)
* [Dear ImGui](https://github.com/ocornut/imgui) integration
* CMake build system
