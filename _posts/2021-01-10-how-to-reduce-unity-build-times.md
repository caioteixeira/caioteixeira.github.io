---
title: "How to reduce Unity build times"
excerpt_separator: "<!--more-->"
classes: wide
categories:
  - Blog
tags:
  - Unity3D
  - Optimization
  - Continuous integration
  - Build automation
published: true
excerpt: Large Unity projects can take a long time to build, in this post I describe some good ways to optimize build times that I learned with my past projects.
---

One of the most common pains of large Unity projects is how a build can take a lot of time to finish. This is particularly painful because it directly affects our ability to quickly iterate in changes. My worst nightmare was having an issue only reproducible on devices, and having to wait one hour to be able to test any small change.

This post is a walkthrough of several different things that you can do to improve the build time of your project. Most of them can be applied to any development or continuous integration environment.
<!--more-->

## Incremental builds

Incremental builds are probably the simplest thing you can do to save time when building projects. The basic idea is that the compiler will only recompile the files that have changed since the last build. To do an incremental build you just need to **build your project in the previous build directory** (without deleting the previous exported build). Yeah, just do this and you will already gain some time back. 🙂

The same idea applies to asset bundles. If you build your asset bundles in the same folder where your last bundles and manifest are, only the bundles with asset changes will be rebuilt. Note that **the manifest file contains all the data that allows Unity to do an incremental build** (like asset lists, content hashes, etc.), so you need to keep it to be able to do an incremental build.

## Split your code into multiple assemblies
Assembly definitions are a long-awaited feature for developers that are using Unity since the stone age (like me). The basic idea is that you can split your source files into multiple separate assemblies, instead of a huge assembly with all the project code. In practice, it allows some interesting things, first of all, **if you change a line of code, only the assembly where this source file is assigned will be rebuilt**, not the entire project (if other assemblies depend on the source file's assembly, they will rebuild too). 

Assemblies can also be built for specific platforms, **you can create one assembly for each platform** instead of adding a lot of [compilation directives](https://docs.unity3d.com/Manual/PlatformDependentCompilation.html) in your code.

![Assemblies per Feature](/assets/images/blog/unity-build/feature-assemblies-examples.png)

Unity has a [great documentation](https://docs.unity3d.com/Manual/ScriptCompilationAssemblyDefinitionFiles.html) on how to setup assembly definitions, it covers many of the problem that you will probably need to handle like dependencies, editor assemblies, constraints, etc.

## Cache your Library folder
When you open a project for the first time, Unity usually takes some time to import all assets that the project has. After importing an asset and doing all the necessary computations, Unity stores the imported asset in the Library folder.
 
![import-asset-meme.gif](/assets/images/blog/unity-build/import-asset-meme.gif)

If you are building your game locally, this big import time only happens for the first time you open a project or switch the build target to a new platform. But if you are building with a continuous integration tool (like **Jenkins**, **Travis CI**, **Github Actions**, or **GitLab CI**) and doing a clean clone for every build, chances are that a considerable slice of your pipeline execution time is actually asset importing.

The obvious idea is: what if we cache the Library folder? 🙂

Caching the Library folder will avoid that your build machine spends a lot of time importing assets for every build. The only caveat is that the **Library folder files use absolute paths**, so your **project path needs to be stable on every build** to get benefits from caching. Common issues are when you have multiple build agents with different users or the path that you clone your project contains a build number.

If you are using GitHub Actions, you can use the [default cache action](https://github.com/marketplace/actions/cache) to cache the Library folder. The following snippet is an example of how you can do it (credits to [GameCI](https://game.ci/docs/github/builder)). You can probably do the same in other CI tools with a similar approach.

```yml
- uses: actions/cache@v1.1.0
  with:
    path: path/to/your/project/Library
    key: Library-MyProjectName-TargetPlatform
    restore-keys: |
      Library-MyProjectName-
      Library-
```

## Build asset bundles and game binary independently

Does your project build new bundles for every new game binary build? You may save a lot of build time by **splitting the asset bundle builds from game binary builds**. Many changes on code will not require entire new asset bundle builds, as assets and scriptable objects will still be compatible and if any compatibility issue happens, you will probably fix it at the same time.

If you are building locally, this can be done as simply as removing your asset bundle build routine from your custom build scripts.

If you are building on a continuous integration tool, you can **cache the folder where you store your built asset bundles** (similarly to how we cached the Library folder) and only **trigger new builds if an asset has changed**. GitHub Actions has a [built-in filter](https://docs.github.com/en/free-pro-team@latest/actions/reference/workflow-syntax-for-github-actions#onpushpull_requestpaths) that you can use to trigger a build action only if an asset in a folder has changed, you can probably do the same in other CI tools.

![Conditional asset bundle build diagram](/assets/images/blog/unity-build/asset-bundle-build-diagram.png)

Another common setup is to actually have a separate project for asset bundles, this way asset and binary builds are completely decoupled. By going this way, you don't have much overhead to import the assets and much of your build time will just be compilation and packaging. This setup is also useful for other reasons, you can implement a decoupled content pipeline for artists and designers, for example.

![How dedicated pipelines would look like](/assets/images/blog/unity-build/asset-bundle-build-dedicated-pipeline-diagram.png)

## Disable Unity modules that you don't use
As you know, Unity has a lot of different subsystems for all the things that we need to create a game like 3D and 2D rendering, physics, UI, monetization, audio, etc. With all these features, Unity by itself has a lot of C# to be compiled on each build and it takes a considerable amount of time to do it. Your project probably doesn't use every subsystem that Unity has implemented, so what if we can **disable Unity subsystems that our projects are not using**?

Since the introduction of [Unity Package Manager](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html), a significant effort has been done by Unity to decouple Unity subsystems and deliver them as packages. So, the cool thing is that we can open the Unity package manager and disable some of the features that we are not using. Let's say your project is a simple 2D game, so you probably don't need any of the 3D physics, terrain, VR, and vehicle subsystems.

To disable a Unity subsystem you first need to **open the Unity Package Manager** window and **select the built-in package**s option on the dropdown. You will see the list of packages that can be disabled and the **disable button at the bottom right.** Many of the packages can be disabled without issues, just be aware that some dependencies between subsystem are not so obvious (last time I checked, scriptable render pipelines had dependencies with physics, for example)

![Built-in packages on Unity package manager window](/assets/images/blog/unity-build/package-manager-window.png)
