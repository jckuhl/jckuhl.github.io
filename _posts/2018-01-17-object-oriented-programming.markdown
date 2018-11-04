---
author: jckuhl
comments: true
date: 2018-01-17 17:20:56+00:00
layout: post
link: https://projectbreakpoint.wordpress.com/2018/01/17/object-oriented-programming/
slug: object-oriented-programming
title: Object Oriented Programming
wordpress_id: 66
categories:
- JavaScript
- programming
- Python
tags:
- object-oriented
---

Object Oriented Programming (OOP) is one of the most useful concepts in programming.  It allows you to bundle your data and the functions your data uses into useful, more concrete objects that make logical sense.  Objects are powerful because they can model real world things.  These things can be something as concrete as a user who logs into your website, or as abstract as the data model behind a statistical analysis of the stock market.

In essence, an object is a individuals bundle of data properties and methods.  In programmer parlance, a property is simply a variable with some value that is tied to an object, and a method is a function that is tied to an object.  Sometimes you'll hear methods also called _member functions _because the function is a member of the object.  Some languages might use other terminology.  Python refers to properties as "attributes."  The words don't matter, the meaning behind them is what's important.

Objects are also individuals.  Each object is its own thing.  You might have five hundred of a specific object, but changing the properties or calling a mutating function on one object, will only affect that one specific object.  Let's say you're playing a video game.  You're a mighty warrior running around in some fantasy world with a sword and you stab an orc in the chest.  The orc takes damage.  Do all the orcs in the game take damage?  No.  Only one orc takes damage, the one you hit.  The orc, as a digital object, is an individual.

Most OOP languages have the concept of classes.  Some, like JavaScript, do not have classes.  Well, JS has "classes" but they're syntactic sugar rather than true classes.  A common misconception is to call a class an "object."  Classes are not objects.  Classes are templates for object construction, or initialization, in the same way that a blueprint is not a house, but a template for building one.  Classes allow us to build our own custom objects, and even if JavaScript does not technically have classes, we can use constructor functions to instantiate objects.  An actual occurrence of an individual object in a program is known as an _instance_ of that object.



```javascript
//This is an object literal in JavaScript
//It is immediately instantiated
const object = {
    property: "some value",
    method: function() {
        console.log(this.property);
    }
}

object.method(); //call the objects member function<

//This is a constructor function in JavaScript
//It is a factory that'll create an object with
//a single property and a single method.
function MyObject(property) {
    this.property = property;
    this.method = ()=> {
        console.log(this.property);
    }
}

//This is how a constructor function is called
//using the new keyword.
//This creates an instance of a MyObject object.
const myObject = new MyObject("Some other value");

myObject.method(); //call the object's member function
```
```python 
#And here's what a class looks like in Python3
#This is a template from which MyObject objects
#can be created.
class MyObject:
    def __init__(self, property):
        self.property = property

    def method(self):
        print(self.property)

#And how a class is used to make an object</span>
#This creates an instance of a MyObject object.
my_object = MyObject("Some pythonic value")
#and we can call its method, or member function
my_object.method()
```


Since code examples can be abstract and tedious, let's consider a real world object as an example.  I find the best example for objects are cars.  Cars have properties and methods.  A car _class _would be the schematics that a car is built from.  But why would we use a schematic, or a class, to create a car?  Why not just build one?  Well, a schematic is reusable.  A schematic says "this is how this particular car is designed" and by following that schematic, we can get multiple reproductions of the same thing.  We can, like in JavaScript, declare a single object literal for a car, but that doesn't work when we want many objects, especially if the number of objects we want is indeterminate.  If we have a website with users and use objects to track those users' information, we can't use literals, because we don't know how many users we're going to have.  We use some sort of schematic, a _class_ or a _constructor function_.

Each car would have its own properties that, while unique to the individual car, every car has.  A car has its own fuel and fuel capacity, a color value for the painting, a certain number of wheels, a certain number of doors.  Every car has these properties but the specific value might be different in individual cars.  For example, one of my coworkers owns a black Subaru WRX.  Another one owns a blue Subaru WRX.  They're both objects from the Subaru WRX class, but their _paint_ property has a different color value.

Cars also have various _methods_.  Methods are, as I said earlier, member functions that belong to an object.  They allow us to black box away some procedure that the object does.  We implement it once, in the class or in the constructor, and then we simply have to call it on that object when we use it in the future.  A car will have an accelerate() function.  You hit the gas, that calls the function and the car's speed increases.  Does it matter how exactly the gas pedal is linked to the engine, or how the engine's throttle opens to admit more gasoline, or how the transmission shifts gears to keep the RPMs low?  No.  Once the car is created, none of that matters (unless you need to fix it.)  You simply hit the gas and go.

Some methods are _static_ in that they are called on the object's type.  A car might have a method called _[convertMpgToKpl()](https://stackoverflow.com/questions/2671496/java-when-to-use-static-methods) _(convert miles per gallon to kilometers per litre.)  Does it matter if you have an actual car?  No.  You don't have to call this function on a car object.  You can call it on the car type itself.  Another example can be found in JavaScript's native library with the [String.fromCharCode()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/fromCharCode) method.  This method is called directly off the string prototype and not off a specific string object.  What fromCharCode() does is it returns a string constructed from the unicode values passed in.  For example, String.fromCharCode(65,66,67) will return "abc".   Since it doesn't matter what string you call fromCharCode() on, why make it necessary to have a string object?  A static method is a method that is associated to an object, defined with in a class, but not specific to an individual object.

Perhaps the most powerful purpose behind objects is the concept of _inheritance_.  Inheritance simply means that the attributes of a super class are inherited by any child classes below it.  So, going back to our cars, we can have a Vehicle class.  A Vehicle class would describe any object with wheels that moves on a road.  That can be a lot of things.  We can have a Car class that inherits from the Vehicle class.  All Cars are Vehicles, but not all Vehicles are cars.  A Semi Truck object, a Bus object, or a BackHoe object are all vehicles, but they would not be cars.  We can take this a step further and go down the chain.  So lets define a car real fast.

A Car has four doors and four wheels.  We can create another class and call it Subaru Outback.  We'll make it a subclass of Car.  A Subaru Outback has four doors and four wheels.  It has its own body shape, unique to all Subaru Outbacks that isn't shared by members of the Car class, but it inherits from Car four doors and four wheels.  A Chevy Cavalier can be another class.  This car also inherits four doors and four wheels.  But it also overrides the four doors property because Chevy Cavaliers (or at least the one I owned) only have two doors.

Inheritance is useful because it allows you to define properties only once.  You can generally define them as high up the inheritance chain as you can go.  Subclasses have access to any methods or properties its parent has.  A Subaru Outback will have an _accelerate()_ function, but that function is likely implemented as high up as the Vehicle class, since any Car, Truck, Bus, Semi, BackHoe, etc, probably all implements acceleration the same way (hit a pedal on the floor with your foot to open a throttle in the engine.)  However, this only works one way.  The superclass cannot access the properties of its subclasses.  And if you think about it, why would it?  Is it important to the definition of a Vehicle what chassis design of your Subaru Outback is?  No!  The Vehicle class doesn't care!  That implementation is too specific.

Then finally we have the concept of polymorphism.  Lets get a new function, _transmission()_.  Transmission does essentially the same thing for every subclass of Car, but the transmission in a Subaru WRX will be implemented differently than the transmission of a Subaru Outback.  WRXs are more powerful and they're standards.  Outbacks are (or at least mine was) automatic.  The class Car might implement a generic transmission method as well, or maybe even as high as the Vehicle class.  The compiler or interpreter that you use can tell, by the type of object calling the transmission() function which one to call.  If it sees SubaruWRX.transmission(), it'll call the method specific to that class.  And if it sees SubaruOutback.transission(), it'll call the transmission defined in SubaruOutback.  Even if there's a transmission() method in the super class (though some languages, like Swift, will require an _override_ keyword.)

In this day and age of programming, objects are everywhere.  Though some people argue that JavaScript is not an object oriented programming language (and they may be technically right,) JavaScript handles objects in its every day while manipulating DOM (Document Object Model) objects in the browser.  If you're playing a video game, you are seeing objects in action.  Those orcs you're farming for felcloth in World of Warcraft are objects in whatever language WoW is programmed in (probably C or C++.)  That Cabal you just shot in Destiny?  Object.  The networking that keeps your XBox connected to Destiny's servers?  Possibly objects.  The models Quicken uses to calculate your taxes?  Could also be objects.

Understanding Object Oriented Programming is vital in today's industries.  The concepts can be abstract at first, but honestly, just think about the actual physical objects around you.  They've got properties, they've got methods.  You can find inheritance in the real world too.  Look at cars.  Every car inherits some property from the definition of Car.  Every refrigerator inherits from the definition of Refrigerator.  When you start relating objects to real world things, they make more sense.

Objects will help you write code that keeps information bundled into specific, and individual bundles.  This will allow you to model almost anything you need to, and it'll keep your code organized and concise.  Take your time with this concept, get to know it.  Understand the concepts behind it.  OOP is a major part of programming and a very valuable tool to have once you wrap your head around it.
