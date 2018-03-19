# 1. The Factory Pattern

A factory is a class responsible for the creation of objects and other types. The factory object usually has an object and methods associated with it. When a client calls this class with certain parameters it will produce an object meeting those descriptors and be returned to the caller. This is in opposition to directly instantiating the object, the reasons to use a factory over this are:

- loose coupling, separating object creation from specific class implementation,
- clients needn't know of the creating class, rather they only need to be aware of the interface, methods and parameters that need to be passed to return the object of desired type,
- the `Factory` class can reuse existing objects whereas direct instantiation will always create new object,
- adding another class to the factory should only require the client to add another parameter.

---------

## Table of Contents

<!-- TOC -->

- [1. The Factory Pattern](#1-the-factory-pattern)
    - [Table of Contents](#table-of-contents)
        - [1.0.1. Simple Factory Pattern](#101-simple-factory-pattern)
        - [1.0.2. Factory Method Pattern](#102-factory-method-pattern)
        - [1.0.3. Abstract Factory Pattern](#103-abstract-factory-pattern)
        - [1.0.4. Which One Do I Use](#104-which-one-do-i-use)
    - [1.1. Summary](#11-summary)

<!-- /TOC -->

---------

### 1.0.1. Simple Factory Pattern

The Simple Factory is not a pattern but a concept for understanding the global factory pattern design theory. Let's look at the simple factory via a code snippet.

```python
# Example 1.1 Simple Factory
# python 3.5 +
from abc import ABCMeta, abstractmethod

class Animal(metaclass = ABCMeta):
    @abstractmethod
    def do_say(self):
        pass

class Dog(Animal):
    def do_say(self):
        print('Woof! Woof!')

class Cat(Animal):
    def do_say(self):
        print('Meeeeeow!')

## forest factory
class ForestFactory(object):
    def make_sound(self, object_type):
        return eval(object_type)().do_say()

## clients code
if __name__ == "__main__":
    ff = ForestFactory()
    animal = input('Choose: Dog or Cat? >>')
    ff.make_sound(animal)
```

Using the `abc` module with its `ABCMeta` and `abstractmethod` prevents the client from directly instantiating the `Animal` class. Instead the client can only create objects via the factory. The `do_say` method is an abstractmethod meaning that this is the only method from the abstract base class that can be called by the client. But it also means the abstractmethod must be located in **all** factory classes. Simply, the factory classes must always have their own methods that are shared with the abstract base class. In this instance the `do_say` simply passes inside `Animal`, and it is the factory classes that define their own unique `do_say` methods. This conforms to the Liskov Substitution principle.

### 1.0.2. Factory Method Pattern

In the last block we discussed the Simple Factory Pattern which is more of a theory than a design - it allows the user to grasp the concept. The 'Factory Method` is similar but more complicated. 

The Factory Method points of note:

- Factory creation is done via inheritance, not through instantiation,
- An interface is used to create the object but the responsibility is passed to the subclass rather than factory,
- This design pattern is more customisable; it can return the same instance or subclass rather than an object of certain type.

```python
# Example 1.2 Factory Pattern

class ShapeInterface:
    def draw(self):
        pass

class Circle(ShapeInterface):
    def draw(self):
        print('Circle.draw')

class Square(ShapeInterface):
    def draw(self):
        print('Square.draw')

class ShapeFactory:
    @staticmethod
    def get_shape(type):
        if type is 'circle':
            return Circle()
        if type is 'square':
            return Square()
```

The key takeaway here is that it is `ShapeFactory` and not the `Circle` or `Square` classes  that constructs the object.

### 1.0.3. Abstract Factory Pattern

The Factory Pattern defers object creation to the subclasses. The Abstract Factory on the other hand provides the interface to create families of related objects without depending their specific classes. In layman's this pattern isolates the client from the creation of objects giving them access via interface only. 

This is analogues to a large factory that makes two different cars, but has the tooling to create only on type at a time. Each car has a subset of properties that are the same between cars, however their implementation within the subset is different, of course. So for example lets call these two car factories "SedanFactory" and "TruckFactory". Each factory has the same tooling for simplicity; engines, chassis, drivetrain etc. Within these subclasses (tooling/ machines in real life) are the requirements for each type of car, a trucks drivetrain is more robust and powerful than a sedans - but they both have a drivetrain. What does this diatribe mean? Well, whilst each factory shares common attributes, they are different and the choice between which factory to use must be made by the client, at the start (read: runtime). A more software centric idea of when to use Abstract Factories may be a cross platform application. The client device will dictate which "factory" to call upon when accessing the application, serving up the appropriate software for the operating system of the client.
This removes the client from having to directly instantiate the objects and pushes it onto the factory directly.

```python
# Example 1.3 Abstract Factory

from abc import ABCMeta, abstractmethod

class VehicleFactory(metaclass=ABCMeta):
    @abstractmethod
    def wheels(self):
        pass

    @abstractmethod
    def engine(self):
        pass

    def __str__(self):
        print('wheels: {} engine: {}'.format(self.wheels(), self.engine()))
        print()

class SedanFactory(VehicleFactory):
    def wheels(self):
        pass

    def engine(self):
        pass


class TruckFactory(VehicleFactory):
    def wheels(self):
        pass

    def engine(self):
        pass


class FourCylinder(SedanFactory):
    def wheels(self):
        wheels = 4
        return wheels

    def engine(self):
        engine = 'Four cylinder'
        return engine

class SixCylinder(SedanFactory):
    def wheels(self):
        wheels = 4
        return wheels

    def engine(self):
        engine = 'six cylinder'
        return engine


class DieselTruck(TruckFactory):
    def wheels(self):
        wheels = 18
        return wheels

    def engine(self):
        engine = "V12"
        return engine

class ElectricTruck(TruckFactory):
    def wheels(self):
        wheels = 12
        return wheels

    def engine(self):
        engine = "Tesla Truck Motor"
        return engine

if __name__ == "__main__":
    vehicle = int(input('What type of vehicle? (1-sedan, 0-truck)'))

    if vehicle == 0:
        choice = int(input('Diesel or electric? (1-diesel, 0-electric)'))
        if choice == 0:
            factory = ElectricTruck()
            print(factory.__str__())
        elif choice == 1:
            factory = DieselTruck()
            print(factory.__str__())
        else:
            print('choose 0 or 1')

    elif vehicle == 1:
        choice = int(input('Four or Six Cylinders? (1-four, 0-six)'))
        if choice == 0:
            factory = SixCylinder()
            print(factory.__str__())
        elif choice == 1:
            factory = FourCylinder()
            print(factory.__str__())
        else:
            print('choose 0 or 1')

    else:
        print('choose 0 or 1')
```

The above module has a factory class of `VehicleFactory` which the concrete factories `SedanFactory` and `TruckFactory` inherit. The subclasses within the concrete factories then become the interfaces for the user or client to interact with. Whilst this is a over simplified example it paints a easy to understand picture of how it works. 

### 1.0.4. Which One Do I Use

**Factory**:
Use the Factory pattern when there a need to remove the client from the responsibility of directly creating and configuring instances of the product or object by instead offering method to do this. It is a good idea to use this when there is only one product to create.

**Abstract Factory**:
When there is multiple classes of products to be created and they are coupled or represent a logical family the abstract pattern is a better option. Use when delegation of object creation is best served by another class or family of classes.

## 1.1. Summary

The factory is an Object Orientated paradigm that uses a class to create other classes. It uses factory methods as interfaces to create new objects. It's intent is to decouple code from rigid constructs by coding to an interface rather than an implementation. Likewise, it also offers us the ability to logically group classes or objects based on their familial ties. This enhances product compatibility and can help to decrease high coupling issues.
