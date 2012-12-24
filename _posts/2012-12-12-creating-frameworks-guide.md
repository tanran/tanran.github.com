--- 
layout: post
title: Creating Frameworks Guide
date: 2012-12-12 17:46:07
categories:
    - 
tags:
    -
---

##API Naming Guidelines

Elements in the global namespace, such as classes, functions, and global variables should all have unique names.

While two-level namespaces help the dynamic-link editor find the correct symbols at runtime, the feature does not prevent static link errors from occurring because of multiply-defined symbols. You should try to choose names that clearly associate each symbol with your framework. For example, consider adding a short prefix to all external symbol names. Typical prefixes include the first couple of letters or an acronym of your framework name. 

If you are writing a category for an Objective-C class, you should also use some sort of unique prefix in your method names. Because categories can be loaded dynamically from multiple sources, a unique prefix helps ensure that the methods in your category don’t conflict with those in other categories.


##Performance Impacts of Frameworks

Before you actually create a custom framework, you should carefully consider its intended use. There is a certain amount of overhead associated with loading and using frameworks at runtime. While this overhead is not high—especially if your framework and application are prebound—it may be unnecessary in some situations.

Frameworks are most effective when their code is shared by multiple applications. If you are designing a suite of applications, you may create a custom framework to store common code accessed by all applications in the suite. Similarly, you may want to create a private framework internally to separate out generic, reusable code from your application-specific code. Although you may embed this framework in each application you create, you simplify your maintenance of the reusable code.


##What to Include in Your Framework

Frameworks provide shared resources for multiple applications. If you are defining a custom framework, you should keep that goal in mind. As much as possible, your framework should not contain code that is tied to a particular application. Instead, it should contain code that is reusable or that is common to multiple applications.

In addition to code, you can include other types of resources in your frameworks. In the Resources directory of your framework, you can include nib files, images, sound files, localized text, and any other type of resource that you might find in an application. At runtime, applications load your framework’s resources using the same bundle mechanism they use for their application-specific resources.


##Using C++ in Framework Code

There are problems inherent with exporting C++ class interfaces from dynamic shared libraries. These problems are the result of a lack of standardized calling conventions for the extensions that differentiate the C++ language from C.

If you need to export class interfaces from your framework, Apple recommends you use Objective-C instead of C++ to define your classes.


##Don’t Create Umbrella Frameworks

While it is possible to create umbrella frameworks using Xcode, doing so is unnecessary for most developers and is not recommended.


##Where to Install Your Framework

OS X looks for public frameworks in several fixed locations on the system. If you are creating a framework for other developers to use, you should put it in one of these locations. If you are creating a private framework, you can either embed it inside an application or put it in a custom location. In either case, you must do some extra work to load your framework code and resources.

---


#Creating a Framework


##Creating Your Framework

###Configuring Your Framework Project

When you create a new framework, there are several configuration options you may want to modify. These options make it easier to distribute your framework to customers and guarantee its compatibility after future development cycles.


###Testing Your Framework in Place

When you build a framework, Xcode places it in the build subdirectory of your project directory by default. 

You can tell your test applications exactly where to find the framework using the **DYLD_FRAMEWORK_PATH** environment variable. Adding this variable to your executable tells dyld where to look for additional frameworks if it doesn’t find what it needs in the standard locations. 


##Embedding a Private Framework in Your Application Bundle

If you need to distribute a private framework with an application, the preferred solution is to embed the framework in your application bundle. Embedding a framework inextricably links the framework to the application and ensures that the application always has the correct version of the framework needed to operate. Embedding the framework also makes it clear to other developers that they should not ever link to that framework.

To embed a framework in an application, there are several steps you must take:

* You must configure the build phases of your application target to put the framework in the correct location.
* You must configure the framework target’s installation directory, which tells the framework where it will live.
* You must configure the application target so that it references the framework in its installation directory.


###Using a Single Xcode Project For Both Targets

Using a single Xcode project for both your application and framework target simplifies the required setup. Once you create your project, you simply add two targets to it: one for your application and one for your framework.

The configuration for your framework target involves telling it where it will be installed. The framework needs this information so that it can find the resources it needs. 

Because frameworks are typically installed in fixed locations, you normally specify the full path to the appropriate frameworks directory. When you embed a framework inside a bundle, however, the location of the framework is not fixed, so you have to use the @executable_path placeholder to let the framework know its location is relative to the current executable.

* Open an inspector for your framework target and select the Build tab.
* Set the value of the Installation Directory build setting to **@executable_path/../Frameworks**.

At build time, Xcode builds your framework and puts the results in the build directory.


###Using Separate Xcode Projects For Each Target

If you already have separate Xcode projects for your framework and application, you can take advantage of Xcode’s cross-project references to embed the framework in your application. Cross-project references are a convenient way to create relationships between two separate Xcode projects. 

Your framework’s installation directory must be configured to be relative to the executable path of the application. Similarly, the application target must copy the framework to its bundle and set up the necessary linkage and dependencies. The only difference is that you must configure each target in its own Xcode project.


##Building Multiple Versions of a Framework

After the release of a framework, you should consider how to manage your Xcode project for future releases. When you update an existing framework, the type of changes you make determines the best way to proceed with your project files. 


###Updating the Minor Version

If you are making minor changes to your framework, there is no need to create a new Xcode project for your framework. However, you should always update the “current version” and “compatibility version” values associated with your framework. 

These values make it possible for the dynamic linker to determine if linking a program to your framework is possible.


###Updating the Major Version

The recommended way to create a new major version is to make a duplicate version of your entire Xcode project folder and continue developing from there. The old project files should be archived and used to perform legacy builds. However, active development should continue with the new project.

Once you have a new project folder, you need to make several modifications to the Xcode project to identify the project as a major version.

During installation, if a version of your framework does not already exist on the target system, have the installer copy your framework bundle over as is. However, if an existing version of the framework is present, have the installer copy the contents of your new framework directory to the old directory. Your installer script must replace symbolic links in the old framework bundle with ones that point to the new version of the framework. However, copying over the new major version should leave any old versions intact. This permits existing applications to continue running while newer versions use the updated framework.


---


#Initializing a Framework at Runtime

When a framework is first loaded by a unique process, the system calls the framework’s initialization code. 

Prior to OS X v10.4, framework initialization typically consisted of a single routine that was called when the framework was first loaded; however, in OS X v10.4 and later, the use of **module initializers and finalizers** became the preferred technique. 

The main advantage of **module initializers** is that they can be called after the dynamic linker has a chance to load any other libraries on which the module initializer depends. The same is not true of framework **initialization routines**, which are called immediately on load and may occur before other important modules (like the C++ runtime) are loaded.


##Initialization Routines and Performance

Because all types of initialization routines are called at framework load-time, they are often called while an application is launching. Launch time is generally a bad time to perform large amounts of work because it can make the corresponding application feel sluggish. When writing your initialization routines, try to do as little work as possible to put your framework in a known state.

Remember that if your framework contains static data, that data must be initialized at load-time as well.


##Defining Module Initializers and Finalizers

Module initializers are the preferred way to initialize a framework. A module initializer is a static function that takes no arguments and returns no value. It is declared using the constructor compiler attribute.

As with any static initialization function, you should guard against the function being called twice by placing a guard variable around your initialization code.

Frameworks can define multiple module initializer functions. The dynamic link editor calls any module initializer functions after initializing any static variables but before calling any other functions or methods in your framework. If the code in a module initializer function relies on other libraries, such as the C++ runtime, the dynamic linker loads those libraries prior to calling the function. 

Each module initializer function is called only once when the framework is loaded by a process. Module initializers are executed in the order they are encountered by the compiler.

The symbols for any module initializer functions must not be exported by your framework. By default, Xcode exports all symbols declared in your project’s header files. 

In OS X v10.4 and later, module initializers can access the launch arguments for the current process. A framework might use these arguments to get information about the launch configuration of the application, such as any environment variables or flags passed in on the launch line.

In addition to module initializer functions, you can also define module finalizer functions, which implement any clean up code for your framework. Module finalizers are declared using the destructor compiler attribute. Like module initializers, the symbols for module finalizers must not be exported by your framework. Module finalizers execute in the reverse order that they are encountered by the compiler.


---


#Exporting Your Framework Interface

When you build a framework or application using Xcode, the linker exports all of the symbols defined in your code by default. 

For a shipping framework with many symbols, this can lead to performance problems at runtime. When a framework is loaded, the **dynamic link editor** loads the symbols associated with the framework. 


##Creating Your Exports File

An exports file is a simple text file (.txt or other text file extension) that contains the list of symbols you want to export.

For ANSI C-based code, you can usually just prefix an underscore character to the name of a function or variable to get the symbol name. 

For languages like C++, which uses mangled symbol names, you may need to run the nm tool to get the list of existing symbol names. Run nm with the -g option to see the currently exported symbols. You can then copy the output from the tool and paste it into your exports file, removing any extraneous information.

You can temporarily remove a symbol from your exports file by putting a pound sign at the beginning of the line containing the symbol.


##Specifying Your Exports File


---


#Installing Your Framework

Once your framework is ready to go, you need to decide where to install it. Where you install a framework also helps determine how to install the framework.


##Locations for Public Frameworks

Third-party frameworks can go in a number of different file-system locations, depending on certain factors.

* Most public frameworks should be installed at the local level in /Library/Frameworks.
* If your framework should only be used by a single user, you can install it in the ~/Library/Frameworks subdirectory of the current user; however, this option should be avoided if possible.
* If they are to be used across a local area network, they can be installed in /Network/Library/Frameworks; however, this option should be avoided if possible.

For nearly all cases, installing your frameworks in /Library/Frameworks is the best choice. Frameworks in this location are discovered automatically by the compiler at compile time and the dynamic linker at runtime.

Third-party frameworks should never be installed in the /System/Library/Frameworks directory. Access to this directory is restricted and is reserved for Apple-provided frameworks only.

The compiler writes path information for any required frameworks in the executable file itself, along with version information for each framework. When the application is run, the dynamic link editor tries to find a suitable version of each framework using the paths in the executable file.

If it cannot find a suitable framework in the specified location (perhaps because it was moved or deleted), it looks for frameworks in the following locations, in this order:

* The explicit path to the framework that was specified at link time.
* The /Library/Frameworks directory.
* The /System/Library/Frameworks directory.


##Locations for Private Frameworks

Custom frameworks intended for internal use should be installed inside the application that uses them. Frameworks embedded in an application are stored in the Frameworks directory of the application bundle. The advantage of embedding frameworks in this manner is that it guarantees the application always has the correct version of the framework to run against.

The limitation of embedding frameworks is that you cannot share the framework among a suite of applications.


##Installing Frameworks

How you install frameworks depends on your framework. If your framework is bundled inside of a particular application, there is nothing special you need to do.

If your framework is external to an application, you should use an installation package to make sure it is put in the proper location. You should also use an installation package in situations where an older version of your framework might be in place. In that case, you might want to write some scripts to update an existing framework bundle rather than replace it entirely.


---


#Including Frameworks

For OS X software developers the guideline for including header files and linking with system software is straightforward: add the framework to your project and include only the top-level header file in your source files. For umbrella frameworks, include only the umbrella header file.


##Including Frameworks in Your Project

You include framework header files in your code using the #include directive. If you are working in Objective-C, you may use the #import directive instead of the #include directive. The two directives have the same basic results. but the #import directive guarantees that the same header file is never included more than once. 

When including framework header files, it is traditional to include only the master framework header file. The master header file is the header file whose name matches the name of the framework.


##Locating Frameworks in Non-Standard Directories

If your project links to frameworks that are not included in any of the standard locations, you must explicitly specify the location of that framework before Xcode can locate its header files. 

To specify the location of such a framework, add the directory containing the framework to the “Framework Search Paths” option of your Xcode project. Xcode passes this list of directories to the compiler and linker, which both use the list to search for the framework resources.


##Headers and Performance

If you are worried that including a master header file may cause your program to bloat, don’t worry.

Because OS X interfaces are implemented using frameworks, the code for those interfaces resides in a dynamic shared library and not in your executable. In addition, **only the code used by your program is ever loaded into memory at runtime**, so your in-memory footprint similarly stays small.

As for including a large number of header files during compilation, once again, don’t worry. Xcode provides a **precompiled header facility** to speed up compile times. By **compiling all the framework headers at once**, there is no need to recompile the headers unless you add a new framework. In the meantime, you can use any interface from the included frameworks with little or no performance penalty.


##Restrictions on Subframework Linking

OS X includes two mechanisms for ensuring that developers link only with umbrella frameworks. One mechanism is an Xcode feature that prevents you from selecting subframeworks. The other mechanism is a compile-time error that occurs when you attempt to include subframework header files.