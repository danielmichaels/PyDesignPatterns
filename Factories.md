## The Factory Pattern

A factory is a class responsible for the creation of objects and other types. The factory object usually has an object and methods associated with it. When a client calls this class with certain parameters it will produce an object meeting those descriptors and be returned to the caller. This is in opposition to directly instantiating the object, the reasons to use a factory over this are:

- loose coupling, seperating object creation from specific class implementatoin,
- clients needn't know of the creating class, rather they only need to be aware of the interface, methods and parameters that need to be passed to return the object of desired type,
- the `Factory` class can reuse existing objects whereas direct instantiation will always create new object,
- adding another class to the factory should only require the client to add another parameter.

<!-- INSERT TOC HERE -->

### Simple Factory Pattern

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

### Factory Method Pattern

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

The key takeway here is that it is `ShapeFactory` and not the `Circle` or `Square` classes  that constructs the object. 

### Abstract Factory Pattern

The Factory Pattern defers object creation to the subclasses. The Abstract Factory on the other hand provides the interface to create families of related objects without depending their specific classes. In laymans this pattern isolates the client from the creation of objects giving them access via interface only. 

This is analogues to a large factory that makes two different cars, but has the tooling to create only on type at a time. Each car has a subset of properties that are the same between cars, however their implementation within the subset is different, of course. So for example lets call these two car factories "SedanFactory" and "TruckFactory". Each factory has the same tooling for simplicity; engines, chassis, drivetrain etc. Within these subclasses (tooling/ machines in real life) are the requirements for each type of car, a trucks drivetrain is more robust and powerful than a sedans - but they both have drivetrains. What does this diatribe mean? Well, whilst each factory shares common attributes, they are different and the choice between which factory to use must be made by the client, at the start (read: runtime). A more software centric idea of when to use Abstract Factories may be a cross platform application. The client device will dictate which "factory" to call upon when accessing the application, serving up the appropriate software for the operating system of the client.
This removes the client from having to directly instantiate the objects and pushes it onto the factory directly.

```python
# Example 1.3 Abstract Factory
