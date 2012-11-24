--- 
layout: post
title: SDK Compatibility Guide
date: 2012-11-24 11:30:13
categories:
    - 
tags:
    -
---

Xcode includes software development kits (SDKs) that enable you to create applications that run on specific versions of iOS or OS X—including versions different from the one you are developing on. This technology lets you build a single binary that takes advantage of new features when running on a system that supports them, and gracefully degrades when running on an older system. Some Apple frameworks automatically modify their behavior based on the SDK an application is built against for improved compatibility.


#Overview of SDK-Based Development

Apple makes SDKs available for specific versions of iOS and OS X. Using these SDKs allows you to build against the headers and libraries of an operating system version other than the one you're running on.

The OS X SDKs are installed as **part of the Xcode Essentials install package** with **Xcode 3.2** and later. 

Take advantage of SDK-based development in these ways:

* You can build a target optimized for one version of an operating system and forward-compatible with later versions, but doesn’t take specific advantage of newer features. (**forward compatibility**)
* You can build a target for a range of operating system versions, so that it can launch in older versions but can take advantage of features in newer ones. (**backward compatibility**)

To develop software that can be deployed on, and take advantage of features from, different versions of iOS or OS X, you specify which version—or SDK—of iOS or OS X headers and libraries to build with (Base SDK). You can also specify the oldest iOS or OS X system version on which the software will run (Deploy target).


##Behavior Selection in Frameworks

As frameworks evolve through various releases, APIs are introduced or deprecated and behaviors of existing APIs may occasionally change.

As a **backward-compatibility mechanism**, Apple frameworks sometimes check for the version of the SDK an application is built against, and, if it is an older SDK, modify the behavior for compatibility.

Typically, frameworks detect how an application is built by looking at the **version of the system frameworks** the application is linked against.

In some cases, frameworks provide **defaults (preferences) settings** that you can use to get the old or new behavior, independent of the SDK an application is built against. Often these preferences are provided for **debugging purposes only**; in some cases the preferences can be used **globally to modify the behavior** of an application by registering the values. If taking advantage of this mechanism, do it very early in your code, using the **NSUserDefaults** method *registerDefaults:*).


---


#Configuring a Project for SDK-Based Development

This chapter describes the configuration of installed iOS and OS X SDKs and explains how to set up your Xcode project to use a particular SDK.


##SDK Header Files and Stub Libraries

When you install Xcode, the installer creates a /Developer/SDKs directory. This directory contains one or more subdirectories, each of which provides the complete set of header files and stub libraries that shipped for a particular version of iOS or OS X.

An OS X SDK is **named by major version**, such as MacOSX10.6.sdk, but represents the latest minor version available for the major version.

The Xcode installer for iOS places SDKs in the /Developer/Platforms directory, within which is a directory for each platform, such as iPhoneOS.platform. Each platform directory, in turn, contains a Developer/SDKs directory specific to that platform. 

The iOS SDKs are **named by minor versions** of iOS, such as iPhoneOS4.2.sdk.

Choosing the latest SDK for your project lets you use the new APIs introduced in the OS update that corresponds to that SDK.  (Base SDK)

The libraries in an iOS or OS X SDK are stubs for **linking only**; they do not contain executable code but just the exported symbols. SDK support works only with native build targets.


##Base SDK and Deployment Target Settings

To use a particular SDK for an Xcode project, make two selections in your project’s build settings. These choices determine which operating system features your project can use, as follows:

* Choose a **deployment target**. This identifies the earliest OS version on which your software can run. The Xcode build variable names for this setting are **MACOSX_DEPLOYMENT_TARGET** (OS X Deployment Target) and **IPHONEOS_DEPLOYMENT_TARGET** (iOS Deployment Target). You can **unconditionally use** features from OS versions up to and including your deployment target setting. 
* Choose a **base SDK**. Your software can use features available in OS versions up to and including the one corresponding to the base SDK. By default , Xcode sets this to the newest OS supported by Xcode.
The Xcode build setting name for this parameter is **SDKROOT** (Base SDK).

When you build your application, your deployment target is reflected in the **MinimumOSVersion** entry in the application’s **Info.plist** file. For iOS apps, the MinimumOSVersion entry is used by the App Store to indicate the iOS release requirement.

Always check to see if you are using deprecated APIs; though still available, deprecated APIs are not guaranteed to be available in the future. 

In addition to setting the base SDK and deployment target for your project as a whole, you can set these values individually for each build target. **Target settings override project settings**. 


##Weak Linking and Apple Frameworks

The Xcode compiler uses availability macros, attached to the symbols in Apple framework headers, to determine whether symbols are weakly or strongly linked. These macros state in which version of the operating system a feature first appeared. 

When a symbol in a framework is defined as weakly linked, the symbol does not have to be present at runtime for a process to continue running. The **static linker** identifies a weakly linked symbol as such in any code module that references the symbol. The **dynamic linker** uses this same information at run time to determine whether a process can continue running.

In the case of a deprecated symbol, the availability macro contains further syntax to indicate the version of the operating system in which the symbol was deprecated. The availability macros are defined in **Availability.h** and **AvailabilityMacros.h** in the /usr/include/ directory.

The Xcode compiler interprets each availability macro in light of the base SDK and deployment target settings for a project. The compiler uses these settings to assign appropriate values to the **MAC_OS_X_VERSION_MIN_REQUIRED** and **MAC_OS_X_VERSION_MAX_ALLOWED** macros.

Before using any symbol introduced in a version of iOS or OS X that is later than your deployment target, check whether the symbol is available. If the symbol is not available, provide an alternate code path.


##Configuring a Makefile-Based Project

If you have a makefile-based project, you can also take advantage of SDK-based development, by adding the appropriate options to your compile and link commands. Using SDKs in makefile-based projects requires GCC 4.0 or later. 


##Setting the Prefix File

Xcode supports prefix files, header files that are included implicitly by each of your source files when they're built. 

Many Xcode project templates generate prefix files automatically, including umbrella frameworks appropriate to the selected type of application. For **efficiency**, prefix files are **precompiled and cached**, so Xcode does not need to recompile many lines of identical code each time you build your project. You can add directives to import the particular frameworks on which your application depends.

If you are using Objective-C, it’s preferable to use the #import directive rather than #include (which you must use in procedural C programs) because #import guarantees that the same header file is **never included more than once**.


---


##Using SDK-Based Development

This chapter describes SDK-based development techniques to use in your Xcode projects, explaining how you can:

* Use weakly linked classes, methods, and functions to support running on multiple versions of an operating system
* Weakly link an entire framework
* Compile conditionally for different SDKs
* Find uses of deprecated APIs in your code
* Determine the operating system version, or a framework version, at run time


##Using Weakly Linked Classes in iOS

If your Xcode project uses weakly linked classes, you must ensure the availability of those classes at run time before using them. Attempting to use an unavailable class may generate a runtime binding error from the dynamic linker, which may terminate the corresponding process.

Xcode projects that use a base SDK of iOS 4.2 or later should use the NSObject *class* method to check the availability of weakly linked classes at run time. This simple, efficient mechanism takes advantage of the **NS_CLASS_AVAILABLE** class availability macro, available for most frameworks in iOS.

[UIPrintInteractionController class]

To use the class method as shown here, you need to do more than ensure that a framework supports the NS_CLASS_AVAILABLE macro. You must also **configure certain project settings**. With these required settings in place, the preceding code safely tests the availability of a class, even when running on a version of iOS in which the class is not present.

In OS X (and in iOS projects that do not meet the set of conditions just listed), you cannot use the class method to determine if a weakly linked class is available. Instead, use the **NSClassFromString** function.

NSClassFromString (@"NSRegularExpression")


##Using Weakly Linked Methods, Functions, and Symbols

If your project uses weakly linked methods, functions, or external symbols, you **must ensure their availability at run time** before using them.

[UIImagePickerController instancesRespondToSelector:@selector (availableCaptureModesForCameraDevice:)]

Check the availability of an Objective-C property by passing the getter method name (which is the same as the property name) to **instancesRespondToSelector:**.

To determine if a weakly linked C function is available, use the fact that the linker sets the address of unavailable functions to NULL. Check a function’s address—and hence, its availability—by comparing the address to NULL or nil.

CGColorCreateGenericCMYK != NULL

Check the availability of an external (extern) constant or a notification name by explicitly comparing its address—and not the symbol’s bare name—to NULL or nil.


##Weak Linking to an Entire Framework

If you are using a recently added framework—one that became available after your **deployment target**—you must **explicitly weak link** to the framework itself.

When using a framework that is available in your deployment target, you should **require** that framework (and not weakly link it).


##Conditionally Compiling for Different SDKs

If you use one set of source code to build for more than one base SDK, you might need to conditionalize for the base SDK in use. Do this by using **preprocessor directives** with the macros defined in Availability.h.

Notice the use of the numerical value 1050 instead of the symbol __MAC_10_5 in the #if comparison clause: If the code is loaded on an older system that does not include the symbol definition, the comparison still works.


##Finding Instances of Deprecated API Usage

As iOS and OS X evolve, the APIs and technologies they encompass are sometimes changed to meet the needs of developers. As part of this evolution, less efficient interfaces are deprecated in favor of newer ones. Availability macros attached to declarations in header files help you find deprecated interfaces. 

If you compile a project with a deployment target of OS X v10.5 and use an interface tagged as deprecated, the compiler issues a corresponding warning. The warning includes the name of the deprecated interface and where in your code it was used.


##Determining the Version of the Operating System or a Framework

In rare instances, **checking the run time availability of a symbol** is not a complete solution. For example, if the behavior of a method changed from one OS version to another, or if a bug was fixed in a previously available method, it’s important to write your code to take those changes into account.

The technique to use for checking operating system version depends on your target platform, as follows:

* To check the version of iOS at run time.

NSString *osVersion = [[UIDevice currentDevice] systemVersion];

* To check the version of OS X at run time, use the Gestalt function and the System Version Selectors constants.

Alternatively, for many frameworks, you can check the version of a specific framework at run time. To do this, use global framework-version constants—if they are provided by the framework. 

For example, the Application Kit (in NSApplication.h) declares the **NSAppKitVersionNumber** constant which you can use to detect different versions of the Application Kit framework

Similarly, Foundation (in NSObjCRuntime.h) declares the **NSFoundationVersionNumber** global constant and specific values for each version.