--- 
layout: post
title: Cocoa Object With Two-Stage Creation
date: 2012-11-28 09:15:13
categories:
    - 
tags:
    -
---


Cocoa中所有的对象都是继承自NSObject,关于对象创建及初始化的过程中，NSObject封装了基础必要的分配内存和初始化操作，因此我们可以方便的生成新的对象，而不需要考虑太多的操作！(Malloc Heap)

Cocoa中关于对象的创建，可以分成两个步骤：内存分配和初始化操作；如果将这两个步骤放到一个方法中执行，则这个方法为类方法。通过类方法创建对象有个缺点，就是需要考虑各种不同的对象内存分配和初始化情况。例如UIImage中各种类方法操作;面对如此多的类方法创建对象，其头文件中必然有对应的方法定义，并且你子类化该类的话，其子类有必要重新实现上面的各种方法。

Paging&Trashing

在物理内存用完无法用于新建对象的时候，系统会自动将当前不用的对象从物理内存拷贝一份放入硬盘中，并且从内存中释放该对象以便获得足够的内存用于新建对象，但这个操作需要耗费一定的时间；因此，当此操作很频繁的时候会引起一定的性能影响。

NSZone正是为了提高程序性能而引入的，它是将联系比较频繁的对象紧密联系起来，减少因为内存不够用导致频繁从物理内存和硬盘之间的读取和写入操作。

A memory zone is a region of page-aligned memory used by a Cocoa application to store its Cocoa objects. Unlike the traditional application space, a memory zone is allowed to grow whenever necessary. Mac OS X creates a default memory zone for each Cocoa application. 

Although you do have the option of creating additional zones, the default zone is fast and efficient enough for most purposes.

Using independent memory zones for storing/disposing large numbers of objects prevents the default zone from being severely fragmented.