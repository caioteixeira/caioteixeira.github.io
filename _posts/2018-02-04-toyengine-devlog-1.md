---
published: true
type: posts
title: "Toy Engine Devlog #1 - DX12 and Imgui"
permalink: "/blog/toyengine-dx12-imgui"
---

It's been a while since [last time](/blog/toyengine-helloworld), mostly because I spent my last months working a lot and trying to find time to finish this project, so I hadn't the time to write new posts. The good news is that I finished it! :) I am officially a bachelor in Computer Information Systems! 

In this post, I will describe briefly how I implemented my DirectX 12 renderer and integrated Dear Imgui and profiling tool with the engine. In a future post, I intend to write about what I found out by comparing DX 11 and DX 12 performances (the actual outcome of my project).

### DX 12 Renderer
My renderer is not an impressive work on graphics techniques, right now it only renders diffuse textures and applies basic Phong lighting. But the things that I am most proud of are on the "backend" implementation, as DX 12 follows that recent trend of "bare to the metal" API's, I need to handle things like memory management and CPU-GPU synchronization by myself, but I am also able to parallelize my rendering tasks with less pain and get a flexible API for anything that I want to do. 
Most of the renderer design is based on Microsoft's [MiniEngine](https://github.com/Microsoft/DirectX-Graphics-Samples/tree/master/MiniEngine), a great sample project with a lot of cool graphics techniques. I spent a lot of time working to figure out a good way to implement dynamic resources, e.g. constant buffers and dynamic vertex buffers, a great resource that I found is this article about Dynamic Resources on Diligent Engine 2.0: [Implement Dynamic Resources on DX12](https://www.codeproject.com/Articles/1094799/Implementing-Dynamic-Resources-with-Direct-D). The basic idea is that we allocate a big buffer on the GPU and use it as a ring buffer, allocating small chunks as needed and deallocating them when the GPU finishes executing the associated render commands.

![ToyEngineRender.png](/assets/images/ToyEngineRender.png)

### Dear IMGUI
One thing that I wanted to have from the beginning of this project is a good debug UI system. After some search I found the amazing [Dear ImGui library](https://github.com/ocornut/imgui), it's the perfect example of a good library, really simple to integrate, has a lot of samples, it is simple to use, but powerful. For now, I am using it for performance statistics and lighting parameters tweaks, but I intend to implement some basic scene editing tools using it.

![ToyEngineImGUi.png](/assets/images/ToyEngineImGUi.png)

### Profiling
Another thing that I wanted to have is a good profiler, with support to multithreading and an easy to use API. I ended up using [EasyProfiler](https://github.com/yse/easy_profiler) mainly for its easy integration. It's good enough for my use right now, I can easily profile a running executable, dump profiling data to a file, and see what task each worker thread is running.

![Profiler.png](/assets/images/Profiler.png)

### What I will work on next
* **Cleanup:** As I implemented most of it during weekends and had a tight schedule, I am not really satisfied with most of the codebase, so I will spend some time cleaning/refactoring that code.
* **Remove SDL dependency:** I decided to use SDL to handle window creation and input events, but after all, I found out that Win32 API is not that difficult and I may be able to remove that huge dependency from the project.
* **Input implementation:** After SDL removal, I will spend some time working on a simple input system.
* **Shadow Mapping:** I never implemented proper shadows on my class projects, so I will study more about the possible techniques and implement a basic implementation.
* **Physics:** I intend to integrate a physics API, probably Bullet or PhysX. 