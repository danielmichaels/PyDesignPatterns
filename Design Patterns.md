# 1. Design Patterns
<!-- TOC -->

- [1. Design Patterns](#1-design-patterns)
  - [1.1. What are they?](#11-what-are-they)
  - [1.2. Object-orientated Programming (OOP)](#12-object-orientated-programming-oop)
    - [1.2.1. Definitions](#121-definitions)
  - [1.3. OOP Design Principles](#13-oop-design-principles)
    - [1.3.1. The open/close principle](#131-the-openclose-principle)
    - [1.3.2. Inversion of control principle](#132-inversion-of-control-principle)
    - [1.3.3. Interface segregation principle](#133-interface-segregation-principle)
    - [1.3.4. The single responsibility principle](#134-the-single-responsibility-principle)
    - [1.3.5. The substitution principle](#135-the-substitution-principle)

<!-- /TOC -->
## 1.1. What are they?

Design patterns are a "recipe" or "method" for dealing with a problem or implementation set. They provide the user with options for dealing with common programming tasks in a way that is elegant, readable and efficient. They can be categorised in one of three ways:

- Creational patterns:
  - work on how objects can be created,
  - isolate the details,
  - independent of the type of object created,
  - Singleton is an example.
- Structural patterns:
  - focus on simplification of the structure and identifying the relationship between classes and objects,
  - focus on class inheritance and composition,
  - design the structure of objects and classes so that they can achieve larger results,
  - Adapter is an example.
- Behavioural patterns:
  - concerned with the interaction between objects and responsibility of objects,
  - objects should be loosely coupled and able to interact,
  - Observer pattern is the example.

Design patterns key features:

- language neutral meaning they can be implemented across multiple languages,
- dynamic in the sense that occasionally new patterns are implemented or discovered,
- customizable.

## 1.2. Object-orientated Programming (OOP)

Python is a object orientated language in the sense that everything is considered an object. Functions, classes, variables, etc are all *objects* but Python does not make the user write in an object orientated way. Nonetheless, this section will cover some OOP basics before moving on.

### 1.2.1. Definitions

- Objects
  - represent entities in your application,
  - interact amongst themselves,
  - i.e. Car is an entity, so is Person and they both interact to move form location to location.
- Classes
  - have attributes (Data Members),
  - have behaviours (Data Methods),
  - consist of a constructor that provides initial state,
  - are like **templates** in that they can be easily reused.
- Methods
  - represent an objects behaviour,
  - act on attributes (Variables) and implement desired functionality.
- Encapsulation
  - keeps objects behaviour hidden from outside scope, or keeps state information private,
  - disallows clients from actively altering internal state by acting directly in it, instead objects may respond via special methods such as `get` and `set`,
  - Pythons encapsulation is not implicit unlike other languages - more aptly, nothing is private! `__` is used to signify that the method or variable is private.
- Polymorphism
  - meaning an object can provide different implementations of the method based on input parameters,
  - Python's polymorphism is built-in for example `+` can either concatenate to strings or add two integers together based on input.
- Inheritance
  - one class can derive most of its functionality from a parent class,
  - using the base class as a template a new class with altered state or attributes can be instantiated easily.
- Abstraction
  - offers simple interface for clients to interact with class objects and call its methods,
  - abstracts away the complexities of the code base from the client so as to make them unaware of the internal implementations underneath,
  - in Python the instantiation of a class is an abstraction of that class.

```python
# Example abstraction

class Adder:
    def __init__(self):
        self.sum = 0
    def add(self, value):
        self.sum += value

acc = Adder() # <-- abstraction of Adder()
for i in range(10):
    acc.add(i)

print(acc.sum)
```

- Composition
  - combine objects or classes into more complex data structures or software,
  - uses an object to call other member functions in other modules making base functionality available across modules without inheritance,
  - simply put: calling one function (method/ class) from within another,
  - in Python decorators and closures are good examples of composition.

## 1.3. OOP Design Principles

### 1.3.1. The open/close principle

BLUF: Classes and objects should be open for extension but closed for modification.

What does that mean? Write classes or modules in a generic way so that when they need to be extended it does not mean the base classes needs to be refactored or altered. Simply put, an extension of the class should be all that's needed for the new behaviour.

The open/close principle should:

- not change existing classes,
- enhance backwards compatibility.

### 1.3.2. Inversion of control principle

BLUF: High-level modules should not be dependant on low-level modules. They should be dependant on abstractions.

Any two modules should not be dependant on each other in a tight way - the base and dependant module should be decoupled with an abstraction layer in between. Further, details of the class should be represented by abstractions. This leads to easier interaction with dependencies due to the clear layer of abstraction.

### 1.3.3. Interface segregation principle

BLUF: Clients should not be forced to depend on interfaces they do not use.

This relates to designers writing their interfaces well and in a non-forceful way. Good design should have methods that a thoughtfully crafted and only implemented where they relate to functionality.

### 1.3.4. The single responsibility principle

BLUF: A class should have only one reasons to change.

>`There should be one-- and preferably only one --obvious way to do it.`

A class or method should have only one given functionality, if it has more, split them. That said a class can change to accommodate different expected behaviours but if it is undergoing two changes it most definitely should be split.

### 1.3.5. The substitution principle

BLUF: Derived classes must be able to completely substitute the base classes.

Developers should derive classes by extending base classes, and they should be as close as possible to that class. Allowing the base class to be replaced without any code changes.
