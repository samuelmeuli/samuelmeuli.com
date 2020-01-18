---
title: Automating the Release of Electron Apps
date: 2019-11-17
categories: [JavaScript, Electron, GitHub, CI]
---

Using GitHub Actions to automatically build and release your Electron app for macOS, Windows and Linux

<!--more-->

One major advantage of building desktop apps with Electron is that your app works on multiple platforms with a single codebase. Unfortunately, there are some challenges in building an app for multiple operating systems:

- Setting up and building your app on VMs is somewhat cumbersome
- Mac apps can only be built on macOS, which not every developer has access to
- Building Windows apps on other platforms has become more difficult with the release of macOS Catalina, which has dropped support for 32-bit apps and therefore breaks Wine

A nice way to solve such problems is to use the recently launched [GitHub Actions](https://github.com/features/actions), which allows you to run CI/CD workflows on multiple operating systems, including macOS, Windows and Ubuntu, which is exactly what you need for building Electron apps.

For this purpose, I created the [Electron Builder Action](https://github.com/samuelmeuli/action-electron-builder). After setting up the action, your pipeline will automatically build your app on GitHub's servers using [`electron-builder`](https://github.com/electron-userland/electron-builder). When the action detects that you've tagged a commit with a version identifier, it can even release the packaged app for you.

You can read more about using the action [here](https://github.com/samuelmeuli/action-electron-builder), and see an example of how it's used in the repository of my [Mini Diary](https://github.com/samuelmeuli/mini-diary) Electron app.
