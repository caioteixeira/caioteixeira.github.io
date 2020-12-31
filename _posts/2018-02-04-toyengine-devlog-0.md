---
published: true
type: posts
date:   2018-02-04 10:01:01 -0300
title: "Toy Engine Devlog #0 - Hello World!"
permalink: "/blog/toyengine-helloworld"
---

Most of the game programming experiences that I had over the years were working on Unity3D based projects and C#, as it is by far the most used technology on Brazilian studios, but since my period studying at USC and my internship at Activision, I noticed that what really makes my eyes shine is lower-level engine programming and optimization. 2017 was the year of my graduation, and for my graduation project I decided to work on a simple game/graphics engine with the goal of learning more about graphics programming using one of the new closer-to-metal graphics APIs, and as I had some experience with DirectX on class projects, I decided to focus on DX 12. 
With my graduation, I will continue to work on this project, I do not intend to make it a “production ready” engine or use it on commercial games, instead, I will just use it as a platform for my future graphics and engine programming learning experiments, and ideally, write about my adventures here :) This first post is a write-up of what I did during the last months, nothing really impressive, but it was for sure a great learning experience.

### DX 12 Renderer
My renderer is actually not an impressive work on graphics techniques, right now it only renders diffuse textures and applies basic Phong lighting. But the things that I am most proud of are on the "backend" implementation, as DX 12 follows that recent trend of "bare to the metal" API's, I need to handle things like memory management and CPU-GPU synchronization by myself, but I am also able to parallelize my rendering tasks with less pain and get a flexible API for anything that I want to do. 
Most of the renderer design is based on Microsoft's [MiniEngine](https://github.com/Microsoft/DirectX-Graphics-Samples/tree/master/MiniEngine), a great sample project with a lot of cool graphics techniques. I spent a lot of time working to figure out a good way to implement dynamic resources, e.g. constant buffers and dynamic vertex buffers, a great resource that I found is this article about Dynamic Resources on Diligent Engine 2.0: [Implement Dynamic Resources on DX12](https://www.codeproject.com/Articles/1094799/Implementing-Dynamic-Resources-with-Direct-D). The basic idea is that we allocate a big buffer on the GPU and use it as a ring buffer, allocating small chunks as needed and deallocating them when the GPU finishes executing the associated render commands.

![ToyEngineRender.png](/assets/images/ToyEngineRender.png)


### Entity-Component-System
One of the recent "buzzwords" on game programming is the concept of Entity-Component-System runtime models, the idea is really similar to the classic Unity-like Entity-Component model, but with the difference that components are just data containers and actual logic is executed by Systems. Systems will query for components (or groups of components) and process that data, for example, a Physics system will query for Rigid Bodies and Colliders and update the physics world using that data. Besides data and logic separation, another good advantage is that we can model our component's data to be more cache-friendly and maybe even easily process it using SIMD instructions.
That whole concept is kind of new to me, so I decided to test it and see if it fits my design goals. So far I am only implementing internal systems, but I think many difficulties will come when I decide to implement some kind of gameplay logic. Some interesting resources that I found about ECS implementations are:
* [Entity Component Samples](https://github.com/sosolimited/Entity-Component-Samples): A good introduction to ECS.
* [Building a Data-Oriented Entity System](http://bitsquid.blogspot.com.br/2014/08/building-data-oriented-entity-system.html): A series of post from the former BitSquid team talking about how they designed a data-oriented ECS implementation.
* [Unite Austin 2017, Writing High-Performance C# Scripts](https://www.youtube.com/watch?v=tGmnZdY5Y-E): Unity is also working on their own ECS implementation, this talk shows some future APIs and a demo using them. 
* [Overwatch Gameplay Architecture and Netcode](http://www.gdcvault.com/play/1024001/-Overwatch-Gameplay-Architecture-and): A cool GDC talk from Blizzard, it exposes in details how Overwatch used an ECS-based gameplay architecture, what were the main pains and how they used it to achieve a good netcode implementation.

### Dear IMGUI
One thing that I wanted to have from the beginning of this project is a good debug UI system. After some search I found the amazing [Dear ImGui library](https://github.com/ocornut/imgui), it's the perfect example of a good library, really simple to integrate, has a lot of samples, it is simple to use, but really powerful. For now, I am using it for performance statistics and lighting parameters tweaks, but I intend to implement some basic scene editing tools using it.

![ToyEngineImGUi.png](/assets/images/ToyEngineImGUi.png)

### Profiling
Another thing that I wanted to have is a good profiler, with support to multithreading and an easy to use API. I ended up using [EasyProfiler](https://github.com/yse/easy_profiler) mainly for its easy integration. It's good enough for my use right now, I can easily profile a running executable, dump profiling data to a file and see what task each worker thread is running.

![Profiler.png](/assets/images/Profiler.png)

### What I will work next
* **Cleanup:** As I implemented most of it during weekends and had a tight schedule, I am not really satisfied with most of the codebase, so I will spend some time cleaning/refactoring that code.
* **Remove SDL dependency:** I decided to use SDL to handle window creation and input events, but after all, I found out that Win32 API is not that difficult and I may be able to remove that huge dependency from the project.
* **Input implementation:** After SDL removal, I will spend some time working on a simple input system.
* **Shadow Mapping:** I never implemented proper shadows on my class projects, so I will study more about the possible techniques and implement a basic implementation.
* **Physics:** I intend to integrate a physics API, probably Bullet or PhysX. 
