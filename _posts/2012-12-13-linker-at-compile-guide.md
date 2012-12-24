--- 
layout: post
title: Linker at Compile Guide
date: 2012-12-13 10:34:40
categories:
    - 
tags:
    -
---

##Two-level namespace

By default **all references resolved to a dynamic library** record the library to which they were resolved. At runtime, **dyld** uses that information to directly resolve symbols.

The alternative is to use the **-flat_namespace** option.  With flat namespace, the library is not recorded.  At runtime, dyld will search each **dynamic library** in load order when resolving symbols.


##Dynamic libraries undefines

When linking for two-level namespace, ld does not verify that **undefines** in dylibs actually exist.  But when linking for flat namespace, ld does check that all **undefines** from all loaded dylibs have a matching definition.  This is sometimes used to force selected functions to be loaded from a static library.