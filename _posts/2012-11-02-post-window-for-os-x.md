--- 
layout: post
title: POST Window for OS X
date: 2012-11-02 15:05:06
categories:
    - 
tags:
    -
---

Window for OS X Guide

1. How Windows Work

2. How a window is Displayed

3. How Modal Windows Work

4. How Panels Work


##How Windows Work

The NSWindow class defines objects that manage and coordinate the windows an application displays on the screen.

The two principal functions of an NSWindow object are to **provide an area in which NSView objects can be placed** and to **accept and distribute, to the appropriate views, events the user instigates** through actions with the mouse and keyboard. 

An NSWindow object is defined by **a frame rectangle** that encloses the entire window, including its title bar, border, and other peripheral elements (such as the resize control), and by **a content rectangle** that encloses just its content area.

When it’s created, a window automatically creates two views: **an opaque frame view** that fills the frame rectangle and draws the border, title bar, other peripheral elements, and background, and *a transparent content view* that fills the content rectangle.


##How a window is Displayed

Normally, any time a view is marked as needing display, the window makes note of this fact and automatically displays itself shortly thereafter. This automatic display is typically performed on each pass through the event loop, but can be turned off using the setAutodisplay: method.

By default, a window’s views are drawn concurrently.


##How Modal Windows Work

There are two mechanisms for operating an application-modal window or panel. 

1. The first, and simpler, is to invoke the **runModalForWindow:** method of NSApplication, which monopolizes events for the specified window until one of **stopModal, abortModal, or stopModalWithCode:** is invoked, typically by a button’s action method. 


2. The second mechanism for operating a modal window or panel, called a modal session, allows the application to perform a long operation while it still sends events to the window or panel. Modal sessions are particularly useful for panels that allow the user to cancel or modify an operation. To begin a modal session, invoke **beginModalSessionForWindow:** on the application, which sets the window up for the session and returns an identifier used for other session-controlling methods. After the loop concludes, you can remove the window from the screen and invoke **endModalSession:** on the application to restore the normal event loop.


The normal behavior of a modal window or session is to **exclude all other windows and panels from receiving events**. 


Modal windows restrict all action to the window itself and any methods invoked from the window. Modal sessions allow the application to continue an operation while accepting input only through the modal session window. Beyond this, you can use distributed objects to **perform background operations in a separate thread**, while allowing the user to perform other actions with any part of the application.


**[NOTE]** Because AppKit isn’t thread-safe, the background thread should communicate with **a designated object** in the main thread that in turn interacts with the AppKit.

**[NOTE]** Before OS X version 10.6, if a modal window was open, application termination would be prevented if the user attempted to terminate that window’s application. Beginning in OS X version 10.6, you can call setPreventsApplicationTerminationWhenModal: with a value of YES to the same behavior. The default value for this is NO after OS X version 10.6.


##How Panels Work

A panel is a special kind of window, typically serving an auxiliary function in an application.

* By default panels are not released when they’re closed.

* Onscreen panels, except for alert dialogs, are removed from the screen when the application isn’t active and are restored when the application again becomes active. 

* Panels can become the key window, but they cannot become the main window.

* If a panel is the key window and has a close button, it closes itself when the user presses the Escape key.


##How Window Controllers Work

A controller object (an instance of the NSWindowController class) manages a window; this object is usually stored in a nib file. This management entails the following:

* Loading and displaying the window
* Closing the window when appropriate
* Customizing the window’s title
* Storing the window’s frame (size and location) in the defaults database
* Cascading the window in relation to other document windows of the application

Although a window controller can manage a programmatically created window, it usually manages a window in a nib file. The nib file can contain other top-level objects, including other windows, but the window controller’s responsibility is this primary window. The window controller is usually the owner of the nib file, even when it is part of a document-based application.


####Window Closing Behavior

* When a window is closed and it is part of a document-based application, the document removes the window’s window controller from its list of window controllers. This results in the system deallocating the window controller and the window, and possibly the NSDocument object itself.

* When a window controller is not part of a document-based application, closing the window does not by default result in the deallocation of the window or window controller.