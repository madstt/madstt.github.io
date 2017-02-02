---
layout: post
title: Squirrel - The Basics
date: 2016-03-11
categories:
- squirrel
published: false
---


In this post I'll cover the steps to create and deploy a Windows installer with automatic update.

On a recent project we've created a WPF client. We needed an installer that was able to be installed on clients machine running Citrix. I've previously written about [Squirrel](https://madstt.dk/squirrel-the-basic/) and it seemed the obvious choice:

- Coded update logic
- Can be created using Team City
- Is able to run in a Citrix environment

The flow is 

## Installer Creation
We're using [Team City](https://www.jetbrains.com/teamcity) as our CI server, so we needed to make Team City create the an installer package  
