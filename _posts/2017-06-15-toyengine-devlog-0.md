---
published: true
type: posts
title: "Toy Engine Devlog #0 - Hello World!"
permalink: "/blog/toyengine-helloworld"
---

During the past weeks, I have been working on a toy game/graphics engine in my free time (which is basically weekends and holidays) as part of my bachelor thesis. It is not intended to be a “production-ready” engine or to be used on commercial games, but to be a platform for my graphics and engine programming experiments, for my thesis it will be used to test and benchmark some basic parallel rendering techniques with Direct3D 11 and Direct3D 12. I am also using this opportunity to test some interesting “buzzwords” that have been popular in the game development community, like Entity-Component-Systems (ECS), data-oriented design, and task-based parallelism. I will try to post the interesting things about the development of this toy engine on some regular dev logs.
### What I have done so far

Most of what I have been doing so far is about the core architecture of the toy engine, like defining interfaces, implementing a lot of helpers for logging, error handling, asset loading, etcetera. It’s been a while since the last time that I coded something using C++, so I am also taking some time to remember and learn new stuff about the language.
### ECS
First of all, I decided that I will use ECS as my runtime model, the basic goal is data and logic separation, so components are just data containers (stored on cache-friendly storages), each entity has some components (for example, a character may have a transform component, a mesh component, a health component, a collision volume, and many others) and each system will query entities that have some specific components, a physics system, for example, can query for entities that have collision volumes and check for collisions. If you want to know more details about ECS implementations, [this article](https://github.com/sosolimited/Entity-Component-Samples) is awesome.

I decided to not spend much time working on my own ECS implementation, so I am using a great open-source ECS library: [entityX](https://github.com/alecthomas/entityx). I am not using it as an external library, but part of my core code, as I am modifying it a little bit, renaming some methods to follow the code style that I defined and sometime in the future I want to integrate a message queue system for communication between systems and even change its implementation if I think it can be improved.
### Renderers
Because of the “close to the metal” approach of Direct3D 12, I decided to not implement a single API-agnostic wrapper for both API’s as most game engines do, instead, I am working on two different renderers, a higher level one implemented with D3D 11 and another one getting full advantage of the lower-level API of Direct3D 12 (at least, it is the plan). Both renderers share a very simple interface with the rest of the engine, basically, an ECS System (the rendering system) will fill a data container (the frame packet) with all the necessary data to render one frame, like meshes references, illumination data, and timing information, that packet will be submitted to the active renderer that will then render the entire frame. I chose this approach inspired by this [awesome GDC talk from Bungie](http://gdcvault.com/play/1021926/Destiny-s-Multithreaded-Rendering), as I intend to fully parallelize my implementation, it seems reasonable to do that data extraction phase and let the renderer process it. I may write a detailed post about that architecture when it becomes more mature.

I am using the awesome opensource [tinyobjloader](https://github.com/syoyo/tinyobjloader) library to load obj files for meshes and MTL file for Phong materials, it is really simple to use and I was successfully able to load big scenes like [rungholt](http://graphics.cs.williams.edu/data/meshes.xml) using it. Right now, I am testing with a very preliminary version of the DirectX 11 renderer, it is using a simple shader that applies the diffuse texture on the mesh. It is very basic, but I think it is enough to test if my frame packet implementation is working properly. Those are two screenshots of it rendering [house and lost-empire scenes](http://graphics.cs.williams.edu/data/meshes.xml):

![House scene render](/assets/images/blog/toy-engine-devlog/house-scene.png)
![Lost Empire scene render](/assets/images/blog/toy-engine-devlog/lost-empire-scene.png)

Currently, I am working on the DirectX 12 renderer. Its “close to the metal” approach is very powerful, but it is also harder to get stuff working efficiently. Managing resource allocations and transitions is hard and I am particularly trying to choose the best way to handle dynamic buffers, I guess I will end up using a ring buffer like this article one.

### What I will do next
I still have a LOT of things to do until the end of my thesis (wish me luck). Those are some of them:
* DirectX 12 renderer: its implementation will need to at least match the basic DirectX 11 implementation
* Profiling code: I still need to define how I will profile CPU and GPU times, I think I will first spread PIX’s markers through my rendering code.
* [Dear IMGUI](https://github.com/ocornut/imgui) integration: I plan to use this awesome immediate-mode GUI library to display debugging information and allow small configuration tweaks at runtime.
* Better shaders: Implement proper shaders and materials, I want to use MTL files data to implement at least a subset of Blinn-Phong shading with proper illumination.
* Thread Pool: Implement or choose a good thread pooling library.