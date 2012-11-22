--- 
layout: post
title: Framework Programming Guide
date: 2012-11-22 09:48:36
categories:
    - 
tags:
    -
---

##Introduction

OS X makes extensive use of frameworks to distribute shared code and resources, such as the interfaces to the system itself. The OS provides support for loading items from a framework directory, ensuring only one copy is in memory at any time.


##What are Frameworks?

A framework is a **hierarchical directory** that encapsulates shared resources, such as a dynamic shared library, nib files, image files, localized strings, header files, and reference documentation in a single package. 

Multiple applications can use all of these resources **simultaneously**. The system loads them into memory as needed and shares the **one copy** of the resource among all applications whenever possible.

Frameworks serve the same purpose as static and dynamic shared libraries, that is, they provide a library of routines that can be called by an application to perform a specific task. 

Frameworks offer the following advantages over static-linked libraries and other types of dynamic shared libraries:

* Frameworks **group** related, but separate, resources together. This grouping makes it easier to install, uninstall, and locate those resources.
* Frameworks can include a wider variety of **resource types** than libraries. For example, a framework can include any relevant header files and documentation.
* Multiple **versions** of a framework can be included in the same bundle. This makes it possible to be backward compatible with older programs.
* Only **one copy** of a framework’s read-only resources reside physically in-memory at any given time, regardless of how many processes are using those resources. This sharing of resources reduces the memory footprint of the system and helps improve performance.

The Darwin layer contains many static and dynamic libraries but otherwise, most OS X interfaces are packaged as frameworks. 

Private frameworks are appropriate for code modules you want to use in your own applications but do not want other developers to use. Public frameworks are intended for use by other developers and usually include headers and documentation defining the framework’s public interface.


##Anatomy of Framework Bundles

Frameworks are packaged in a bundle structure. The framework bundle directory ends with the .framework extension, and unlike most other bundle types, a framework bundle is presented to the user as a directory and not as a file. 


###Framework Bundle Structure

Framework bundles use a bundle structure different from the bundle structure used by applications. The structure for frameworks is **based on an earlier bundle format**, and **allows for multiple versions** of the framework code and header files to be stored inside the bundle. This type of bundle is known as a versioned bundle.


####Framework Versions

When you build a new framework project in Xcode, the build environment creates a **versioned bundle structure** for you automatically.

Each real directory in Versions contains a specific **major version** of the framework. Earlier major versions of the framework are needed by clients created when those versions were current at the time. New major versions of a framework are required only when changes in the framework’s dynamic shared library would prevent a program linked to it from running. Programs built using the earlier major version of the library must continue to use it, but programs in development should link against the current version of the library.



####Additional Directories

Frameworks typically include more directories than just the Resources directory. For example, Headers, Documentation, and Libraries.

* **Headers**   Contains any public headers you want to make available to external developers.
* **Documentation**    Contains HTML or PDF files describing the framework interfaces
* **Libraries**    Contains any secondary dynamic libraries your framework requires.

To create additional directories in your framework, you must add build phases to the appropriate target in Xcode.

The **Copy Files** build phase lets you create directories and copy selected files into those directories.


####Framework Configuration

Frameworks require the same sort of configuration as any other type of bundle.

In the information property list for your framework, you should include the keys.

* CFBundleName
* CFBundleIdentifier
* CFBundleVersion
* CFBundleSignature
* CFBundlePackageType
* NSHumanReadableCopyright
* CFBundleGetInfoString


###Umbrella Framework Bundle Structure

The structure of an umbrella framework is similar to that of a standard framework, and applications do not distinguish between umbrella frameworks and standard frameworks when linking to them.

However, two factors distinguish umbrella frameworks from other frameworks. The first is **the manner in which they include header files**. The second is **the fact that they encapsulate subframeworks**.


####The Purpose of Umbrella Frameworks

The purpose of an umbrella framework is to **provide all the necessary interfaces for programming** in a particular application environment. Umbrella frameworks hide the complex cross-dependencies among the many different pieces of system software. 

Umbrella frameworks also **make faster builds possible** through the use of precompiled headers.

An umbrella framework simply includes and links with constituent subframeworks and other public frameworks. An umbrella framework encompasses all the technologies and APIs that define an application environment or a layer of system software. It also provides a layer of abstraction between what outside developers link their programs with and what Apple engineering provides as implementation.


####The Umbrella Framework Bundle

Physically, umbrella frameworks have a similar structure to standard frameworks. One significant difference is the **addition of a Frameworks directory** to contain the subframeworks that make up the umbrella framework.


---


You can create different versions of a framework based on the type of changes made to its dynamic shared library. There are two types of versions: major (or incompatible) and minor (or compatible) versions.


##Major Versions

A major version of a framework is also known as an incompatible version because it breaks compatibility with programs linked to a previous version of the framework’s dynamic shared library. Any such program running under the newer version of the framework is likely to experience runtime errors because of the changes made to the framework.


###Major Version Numbering Scheme

Because all major versions of a framework are kept within the framework bundle, a program that is incompatible with the current version can still run against an older version if needed.

The path of each major version encodes the version.

/System/Library/Frameworks/Boffo.framework/Versions/A/Boffo

When a program is built, the linker records this path in the program executable file. The dynamic link editor uses the path at runtime to find the compatible version of the framework’s library. 

Thus the major versioning scheme enables backward compatibility of a framework by including all major versions and recording the major version for each executable to run against.


###When to Use Major Versions

You should make a new major version of a framework or dynamic shared library whenever you make changes that might break programs linked to it.


###Avoiding Major Version Changes

Creating a major version of a framework is something that you should avoid whenever possible. Each new major version of a framework requires more storage space than a comparable minor version change. 

The following list shows ways to incorporate features without requiring a new major version:

* Pad classes and structures with reserved fields.
* Don’t publish class, function, or method names unless you want your clients to use them. 
* Don’t delete interfaces.
* Remember that if you add interfaces rather than change or delete them, you don't have to change the major version number because the old interfaces still exist.


###Creating a Major Version of a Framework

A popular convention for this designator is the letters of the alphabet, with each new version designator “incremented” from the previous one. However, you can use whatever convention is suitable for your needs, for example “2.0” or “Two”.


##Minor Versions

Minor versions are also referred to as “compatible versions” because they retain compatibility with the applications linked to the framework.

Minor versions don’t change the existing public interfaces of the framework. Instead, they add new interfaces or modify the implementation of the framework in ways that provide new behavior without changing the old behavior.

Within any major version of the framework, only one minor version at a time exists. Subsequent minor versions simply overwrite the previous one.


###Minor Version Numbering Scheme

Frameworks employ two separate numbers to track minor version information. 

The current version number tracks individual builds of your framework and is mostly for internal use by your team. You can use this number to track a group of changes to your framework and you can increment it as often as seems appropriate. A typical example would be to increment this number each time you build and distribute your framework to your internal development and testing teams.

The compatibility version number of your framework is more important because it marks changes to your framework’s public interfaces. When you make certain kinds of changes to public interfaces, you should set the compatibility version to match the current version of your framework. Thus, the compatibility version typically lags behind the current version. Only when your framework’s public interfaces change do the two numbers match up.


###When to Use Minor Versions

You should update the version information of your framework when you make any of the following changes:

* Add a class
* Add methods to an Objective-C class
* Add non-virtual methods to a C++ class
* Add public structures
* Add public functions
* Fix bugs that do not change your public interfaces
* Make enhancements that do not change your public interfaces

Any time you change the public interfaces of your framework, you must update its compatibility version number. 

If your changes are restricted to bug fixes or enhancements that do not affect the framework’s public interfaces, you do not need to update the compatibility version number.


###Compatibility Version Numbers at Runtime

When a program is linked with a framework during development, the linker records the compatibility version of the development framework in the program’s executable file. At runtime, the dynamic link editor compares that version against the compatibility version of the framework installed on the user’s system. 


###Creating a Minor Version of a Framework

If you are developing a framework, you need to increment your current version and compatibility version numbers at appropriate times. 


##Versioning Guidelines

Whenever you change the code in your framework, you should also modify the configuration information in your framework project to reflect the changes. 

For simple changes, such as bug fixes and most new interfaces, you may need to increment only the minor version numbers associated with your framework. 

For more extensive changes, you must create a new major version of your framework.


---


#Frameworks and Binding

Dynamic binding of Mach-O libraries brings a considerable power and flexibility to OS X. Through dynamic binding, frameworks can be updated transparently without requiring applications to relink to them. At runtime, a single copy of the library’s code is shared among all the processes using it, thus reducing memory usage and improving system performance.


##Dynamic Shared Libraries

The executable code in a framework bundle is a dynamically linked, shared library—or, simply, a dynamic shared library. This is a library whose code can be shared by multiple concurrently running programs.

Dynamic shared libraries bring several benefits. 

* One benefit is that they enable memory to be used more efficiently. Instead of programs retaining a copy of the code in memory, all programs share the same copy. 
* Dynamic shared libraries also make it easier for developers to fix bugs in library code. Because the library is linked dynamically, the new library can be installed without rebuilding programs that rely on it.


###Symbol Binding

Dynamic shared libraries have characteristics that set them apart from static linked shared libraries. 

For static linked shared libraries, the symbols in the library are checked at link time to make sure they exist. If they don’t exist, link errors occur. 

With dynamic shared libraries, the binding of undefined symbols is delayed until the execution of the program. More importantly the dynamic link editor resolves each undefined symbol only when the symbol is referenced by the program. If a symbol is not referenced, it is not bound to the program.

The ability to bind symbols at runtime is made possible by the internal structure of Mach-O dynamic shared libraries.

At runtime, the dynamic link editor automatically loads and links modules only as they are needed.

###Organizing Your Framework Code

As a framework developer, you should design your dynamic shared library with this **as-needed linking** of separate modules in mind. 

Because the dynamic link editor always attempts to bind unresolved symbols within the same module before going on to other modules and other libraries, you should ensure that interdependent code is put in its own module. 

When you create a framework, you must ensure that each symbol is defined only once in a library. 

When you build a program, linking it against a dynamic shared library, the installation path of the library is recorded in the program.


###Library Dependencies

Clients of dynamic shared libraries do not need to be aware of any dependencies required by the library. When a dynamic shared library is built, the static linker stores information about any dependent libraries inside the dynamic shared library executable. At runtime, the dynamic link editor reads this information and uses it to load the dependent libraries as needed.

Another important piece of information stored for each dependent library is the required version. Frameworks and dynamic shared libraries have version information associated with them. 

At runtime, the stored version information is compared against the actual version of the available library.


###Standalone Dynamic Shared Libraries

By convention, stand-alone dynamic shared libraries have a .dylib extension and are typically installed in /usr/lib. The library file contains all the code and resources needed by the library.

Creating standalone dynamic shared libraries is an uncommon approach for most developers. In most cases, frameworks are a preferred approach.


##Frameworks and Prebinding

Prior to OS X v10.3.4, OS X used a feature called prebinding to eliminate the load-time delays incurred by executables linked to dynamic libraries. Prebinding involved the precalculation of symbol addresses in each framework and library on the system. The goal of this precalculation was to avoid address-space conflicts among the libraries and frameworks. Such conflicts incurred tremendous performance penalties at load-time and would noticeably slow down the launch time of an application.

Improvements to the dynamic loader in OS X v10.3.4 made prebinding largely unnecessary. The dynamic loader itself was modified to handle load-time conflicts much more efficiently. Using the new dynamic loader, an application that is not prebound now usually launches at least as fast (and sometimes faster) than it did on earlier versions of the system when it was prebound.

In OS X v10.4, another change was introduced to the prebinding behavior to reduce the amount of time spent "optimizing" the system after installing new software. Instead of prebinding all frameworks and libraries, now only select system frameworks are prebound. By selectively choosing which frameworks are prebound, the prebinding tools are able to tightly pack the system's most frequently-used frameworks into a smaller memory space than before. This step reduces the amount of space reserved for Apple frameworks and gives it back to third-party applications and frameworks.

If you are developing frameworks for OS X v10.4 or later, prebinding is not required. Prebinding your framework on later versions of the system does not decrease performance, but does require some additional configuration steps, which are described in the sections that follow.


---


#Frameworks and Weak Linking

When a symbol in a framework is defined as weakly linked, the symbol does not have to be present at runtime for a process to continue running. 

The static linker identifies a weakly linked symbol as such in any code module that references the symbol. The dynamic linker uses this same information at runtime to determine whether a process can continue running. If a weakly linked symbol is not present in the framework, the code module can continue to run as long as it does not reference the symbol. However, if the symbol is present, the code can use it normally.

If you are updating your own frameworks, you should consider making new symbols weakly linked.


##Weak Linking and Apple Frameworks

Apple frameworks use the availability macros to determine whether a symbol is weakly linked or strongly linked. Apple wraps new interfaces in its frameworks with availability macros to indicate which version of the operating system a feature first appeared. Macros are also used to indicate deprecated features and interfaces.

When you create a new project, you tell the compiler which versions of OS X your project supports by setting the deployment target and target SDK in Xcode. The compiler uses these settings to assign appropriate values to the MAC_OS_X_VERSION_MIN_REQUIRED and MAC_OS_X_VERSION_MAX_ALLOWED macros, respectively. 

Before using any symbols that are introduced in a version of OS X that is later than your minimum required version, make sure you check to see that the symbol exists first.


##Marking Symbols for Weak Linking

If you define your own frameworks, you can mark symbols as weakly linked using the weak_import attribute. Weak linking is especially appropriate if you introduce new features to an existing framework. 

To mark symbols as weakly linked, you must make sure your environment is configured to support weak linking:

* You must be using GCC version 3.1 or later. Weak linking is not supported in GCC version 2.95
* You must set the OS X Deployment Target build option of your Xcode project to OS X v10.2 or later.


##Using Weakly Linked Symbols

If your framework relies on weakly linked symbols in any Apple or third-party frameworks, you must check for the existence of those symbols before using them. 

If you attempt to use a non-existent symbol without first checking, the dynamic linker may generate a runtime binding error and terminate the corresponding process.

If a weakly linked symbol is not available in a framework, the linker sets the address of the symbol to NULL. You can check this address in your code.


##Weak Linking to Entire Frameworks

When you reference symbols in another framework, most of those symbols are linked strongly to your code. In order to create a weak link to a symbol, the framework containing the symbol must explicitly add the weak_import attribute to it.

However, if you do not maintain a framework and need to link its symbols weakly, you can explicitly **tell the compiler to mark all symbols as weakly linked**.