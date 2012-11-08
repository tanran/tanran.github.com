--- 
layout: post
title: Handling Key Events
date: 2012-11-08 15:30:58
categories:
    - 
tags:
    -
---


An OS X system generates key events when a user presses a key on a keyboard or presses several keys simultaneously.


##Overview of Key Events

The salient facts about key events are the following:

* Key events are of three specific types (NSEventType)
* Most key events—that is, those representing characters to be inserted as text—are dispatched by the NSWindow object associated with the key window to the first responder.
* If the first responder does not handle the event, it passes the event up the responder chain.
* The delivery path of a key event varies according to whether the event represents a character, a key equivalent, a keyboard action, or a keyboard interface control command.

The global application object (NSApp) first looks for key equivalents and then keyboard interface control commands and handles them specially (see “The Path of Key Events” for details). If the event is neither of these, it dispatches it to the NSWindow object representing the key window, which in turn dispatches the event to the first responder in a keyDown: message.

* The responder object determines what the key event represents and handles it appropriately. At this point, the key event could represent any one of the following things:

1. A keyboard action, which is a key or key combination bound to an action-message selector in a key bindings dictionary (see “Key Bindings”).
2. An application-specific command or action (one not using the key bindings dictionary)
3. A character or characters to insert into text

* As with key event messages, a keyboard action message is passed up the responder chain if the first responder does not handle it.


##Overriding the keyDown: Method

Most responder objects (such as custom views) handle key events by overriding the *keyDown:* method declared by *NSResponder*. 


###Handling Keyboard Actions and Inserting Text

Responder objects that deal with text, such as text views, have to be prepared to handle key events that can either be characters to insert or keyboard actions. 

In handling a key event in keyDown:, a view object that expects to insert text first determines whether the character or characters of the NSEvent object represent a keyboard action. If they do, it sends the associated action message; if they don’t, it inserts the characters as text.

Specifically, the view can do one of two things in its implementation:

* It can extract the event object’s characters using the characters method of NSEvent and interpret these to see if they are associated with a known keyboard action. If they are, it invokes the appropriate action method in itself or a superview. This approach is **discouraged**.
* It can pass the event to Cocoa’s **text input management system** by invoking the NSResponder method *interpretKeyEvents:*. The input management system checks the pressed key against entries in all relevant key-binding dictionaries and, if there is a match, sends a *doCommandBySelector:* message back to the view. Otherwise, it sends an *insertText:* message back to the view, and the view implements this method to extract and display the text.

A case in point of a superview handling a keyboard-action message initiated by a subview is the way the NSScrollView object handles page-down commands. Applications other that those that deal with text can use the input management system to their benefit.

In most instances, the interpretKeyEvents: approach is preferable to the interpret-yourself approach. 


###Specially Interpreting Keystrokes

Although using the text input management system is advantageous, you can interpret physical keys yourself in keyDown: and handle them in an application-specific way.


##Handling Key Equivalents

A key equivalent is a character bound to some view in a window. This binding causes that view to perform a specified action when the user types that character, typically while pressing a modifier key (in most cases the Command key). A key equivalent must be a character that can be typed with no modifier keys, or with Shift only.

An application routes a key-equivalent event by sending it first down the view hierarchy of a window.

The global NSApplication object dispatches events it recognizes as potential key equivalents (based on the presence of modifier flags) in its *sendEvent:* method. It sends a *performKeyEquivalent:* message to the key NSWindow object. This object passes key equivalents down its view hierarchy by invoking the NSView default implementation of *performKeyEquivalent:*, which forwards the message to each of its subviews (including contextual and pop-up menus) until one responds YES; if none does, it returns NO. If no object in the view hierarchy handles the key equivalent, NSApp then sends *performKeyEquivalent:* to the menus in the menu bar. NSWindow subclasses are discouraged from overriding *performKeyEquivalent:*

Some Cocoa classes, such as NSButton, NSMenu, NSMatrix, and NSSavePanel, provide *default implementations* of *performKeyEquivalent:*.However, subclasses of other Application Kit classes (including custom views) need to provide their own implementations of *performKeyEquivalent:*.


##Keyboard Interface Control.

The Cocoa event-dispatch architecture treats certain key events as commands to move control focus to a different user-interface object in a window, to simulate a mouse click on an object, to dismiss modal windows, and to make selections in objects that allow selections. This capability is called keyboard interface control. 

Most of the user-interface objects involved in keyboard interface control are NSControl objects, but objects that aren’t controls can participate as well. When an object has control focus, the Application Kit draws a light-blue key-focus ring around the object’s border. 

Some objects found on Interface Builder palettes do not participate in keyboard interface control, such as NSImageView, WebView, and PDFView objects.

In addition to the key view loop, a window can have a default button cell, which uses the Return (or Enter) key as its key equivalent. The Escape key is another default key for a keyboard interface control in a window; it immediately aborts a modal loop.

The user-interface objects that are connected together in a window make up the window’s key view loop. A key view loop is a sequence of NSView objects connected to each other through their nextKeyView property (the previousKeyView property when going in reverse direction). The last view in this sequence “loops” back to the first view. By default, NSWindow assigns an initial first responder and constructs a key view loop with the objects it finds. 