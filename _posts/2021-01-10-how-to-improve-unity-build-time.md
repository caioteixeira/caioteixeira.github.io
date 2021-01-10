---
title: "How to reduce Unity build times"
excerpt_separator: "<!--more-->"
classes: wide
categories:
  - Blog
tags:
  - Unity3D
  - Continuous integration
  - Build automation
published: false
---

One of the most common pains of large Unity projects is how a build can take a lot of time to finish. This is particularly painful because it directly affects our ability to quickly iterate in changes. My worst nightmare was having an issue only reproducible on devices, and having to wait 1 hour for a build to finish to be able to test any small change.

This post is a walkthrough of several different things that you can do to improve the build time of your project. Most of them can be applied to any development or continuous integration environment.

<!--more-->

## Split your code into multiple assemblies

## Incremental builds

## Cache your Library folder

When you open a project for the first time, Unity usually takes some time to import all assets that the project has. After importing an asset and doing all the necessary computations, Unity stores the imported asset in the Library folder. 

![import-asset-meme.gif](/assets/images/blog/unity-build/import-asset-meme.gif)

If you are building your game locally, this big import time only happens for the first time you open a project or switch the build target to a new platform. But if you are building with a continuous integration tool (like **Jenkins**, **Travis CI**, **Github Actions**, or **GitLab CI**) and doing a clean clone for every build, chances are that a considerable slice of your pipeline execution time is actually asset importing.

The obvious idea is: what if we cache the Library folder? 🙂

Caching the Library folder will avoid that your build machine spends a lot of time importing assets for every build. The only caveat is that the **Library folder files use absolute paths**, so your **project path needs to be stable on every build** to get benefits from caching. Common issues are when you have multiple build agents with different users or the path that you clone your project contains a build number.

If you are GitHub Action, you can use the [default cache action](https://github.com/marketplace/actions/cache) to cache the Library folder. The following snippet is an example of how you can it (credits to [GameCI](https://game.ci/docs/github/builder)). You can probably do the same in other CI tools with a similar approach.
TODO: Add snippet

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

Does your project build new bundles for every new game binary build? You may save a lot of build time by splitting the asset bundle builds from game binary builds. Many changes on code will not require entire new asset bundle builds, as assets and scriptable objects will still be compatible and if any compatibility issue happens, you will probably fix it at the same time.

If you are building locally, this can be done as simply as removing your asset bundle build routine from your custom build scripts.

If you are building on a continuous integration tool, you can cache the folder where your store your built asset bundles (similarly to how we cached the Library folder) and only trigger new asset bundle builds if an asset has changed. GitHub Actions has a [built-in filter](https://docs.github.com/en/free-pro-team@latest/actions/reference/workflow-syntax-for-github-actions#onpushpull_requestpaths) that you can use to trigger an asset bundle build action only if an asset in a folder has changed, you can probably do the same in other CI tools.

![asset-bundle-build-diagram.png](/assets/images/blog/unity-build/asset-bundle-build-diagram.png)


## Disable Unity modules that you don't use

## Remove assets that you don't need