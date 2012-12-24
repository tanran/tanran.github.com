--- 
layout: post
title: Cocoa Framework Guide
date: 2012-11-22 09:31:19
categories:
    - 
tags:
    -
---

Frameworks are hierarchical directory structures grouping related but separate items, for example: dynamic libraries, header files, user interface assets and documentation. They provide an internal versioning facility (defined by the directory hierarchy). The OS provides support for loading items from a framework directory, ensuring only one copy is in memory at any time.

Using a framework in Xcode is as simple as a drag-and-drop operation; header paths and linkage are looked after for you automatically.

A framework usually contains a dynamically loaded shared library, and the associated header files a client application requires to be able to access its facilities.

When shipping a static library, you will also have to ship a library version for each platform the developer will need (at the very least, an ARM code library for use on the iPhone OS device itself, and an i386 build for them to use in the iPhone simulator).


##How to build your own framework

There is one caveat: because of iPhone OS limitations the framework will not be a shared library; it will only provide a statically linked library. But the application writer need not be concerned about this issue. As far as they're concerned everything will just work as if they were using a standard OS framework.

1. Structure your framework's header files.
2. Put your source files elsewhere
3. Create a static library target
4. Create the framework plist file
5. Construct the framework by hand