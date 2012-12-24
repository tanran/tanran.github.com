--- 
layout: post
title: Content or Container View Controller
date: 2012-12-24 16:05:41
categories:
    - 
tags:
    -
---

#Container View Controller

Here are some specific questions you should be able to answer about your container class:

* What is the role of the container and what role do its children play?
* Is there a relationship between siblings?
* How are child view controllers added to or removed from the container? Your container class must provide public properties and methods to allow children to be displayed by it.
* How many children are displayed by the container?
* Are the contents of the container static or dynamic? In a static design, the children are more or less fixed, whereas in a dynamic design, transitions between siblings may occur. You define what triggers a transition to a new sibling. It might be programmatic or it might happen when a user interacts with the container.
* Does the container own any of its own views? For example, your container’s user interface may include information about the child view controller or controls to allow navigation.
* Does the container require its children to provide methods or properties other than those found on the UIViewController class? There are many reasons why a container might do this. It might need specific information from the child to used to configure other aspects of container display, or it might allow the child to modify the container’s behavior. It even might call the child view controller when container-specific events occur.
* Does your container allow its behavior to be configured?
* Are all its children treated identically or does it have multiple types of children, each with specialized behaviors? For example, you might create a container that displays two children, coordinating actions between the two children. Each child implements a distinct set of methods to allow its behavior to be configured.


In summary, a container controller often has more relationships with other objects (especially other view controllers) than a content controller. So, you need to put additional effort into understanding how the container works. Ideally, as with a content controller, you want to hide many of those behaviors behind an excellent public class API.


##Implementing a Custom Container View Controller

Once you’ve designed your class’s behavior and determined many aspects of its public API, you are ready to start implementing the container. The goal of implementing a container is to be able to add another view controller’s view (and associated view hierarchy) as a subtree in your container’s view hierarchy. The child remains responsible for its own view hierarchy, save for where the container decides to place it onscreen. When you add the child’s view, you need to ensure that events continue to be distributed to both view controllers. You do this by explicitly associating the new view controller as a child of the container.


#Content View Controller

##Anatomy of a Content View Controller

###View Controllers Manage Resources

###View Controllers Manage Views

###View Controllers Respond to Events

###View Controllers Coordinate with Other Controllers

###View Controllers Often Work with Containers

###View Controllers May Be Presented by Other View Controllers


##Designing Your Content View Controller

* Are you using a storyboard to implement the view controller?
* When is it instantiated?
* What data does it show?
* What tasks does it perform?
* How is its view displayed onscreen?
* How does it collaborate with other view controllers?


###Use a Storyboard to Implement Your View Controller

###Know When Your Controller Is Instantiated

###Know What Data Your View Controller Shows and Returns

###Know What Tasks Your Controller Allows the User to Perform

###Know How Your View Controller Is Displayed Onscreen

###Know How Your Controller Collaborates with Other Controllers