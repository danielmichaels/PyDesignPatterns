# The Singleton Pattern

This section will cover what Singleton is, some real-world applications of the pattern and how it can be implemented in Python. Alex Martelli's Monostate or Borg pattern will also be discussed and showcased, and why it would be used over the Singleton.

## What is the Singleton Pattern

Singleton provides one and only one object of a given type, and provides a global point of access to that type. The Singleton is often used in classes such as logging or database operators where having only one instance prevents locking issues or other conflicts due to multiple requests. In sum the basic intentions of the Singleton are:

- ensure only one object of the class is created,
- provide a global point of access to that object,
- share controlling concurrent access to resources.

## Implementation: Standard

Given what we know about Singleton now, our Python implementation will do ensure that:

1. We create only one instance of the `Singleton` class,
2. If the instance already exists, we serve that same object again.

```python
# Example 1.1 Singleton

class Singleton:
    def __new__(cls):
        if not hasattr(cls, 'instance'):
            cls.instance = super(Singleton, cls).__new__(cls)
        return cls.instance

s = Singleton()
print('object created', s)

s1 = Singleton()
print('object created', s1)

>>> object created <__main__.Singleton object at 0x7efde8befb00>
>>> object created <__main__.Singleton object at 0x7efde8befb00>

```

In the the above example the `__new__` special method is called, and then overridden. We check if the object already exists using the `hasattr` method and if the `cls` has an attribute named `instance`. If it does not exist then we create the new class with the attributes from the Singleton object and create it using the `__new__`  method. If it does exist the new object will be given the same address in memory as the original and only Singleton object, as per the design.

## Implementation: Lazy

Lazy instantiation allows the object to be created only as its needed. This saves on resources and run times, although it is not as explicit. In Example 1.1 calling `s = Singleton()` creates the object, whereas below in Example 1.2 we will see that is no longer the case.

```python
# Example 1.2 Lazy

class Singleton:
    __instance = None
    def __init__(self):
        if not Singleton.__instance:
            print("__init__ method called")
        else:
            print("instance already created: {}".format(self.getInstance()))

    @classmethod
    def getInstance(cls):
        print("creating object")
        if not cls.__instance:
            cls.__instance = Singleton()
        return cls.__instance

s = Singleton() # class initialised, but no object created.
>>> __init__ method called
s.getInstance() # now we create the object explicitly.
creating object
__init__ method called
<Singleton.Singleton object at 0x7f04d2f506a0>
s1 = Singleton()
>>> instance already created: <__main__.Singleton object at 0x7f4f04db47f0>
```

As we can see above the object is initialised when the class is called but it is not until we call `getInstance` that an object is created. This is how lazy instantiation is achieved.

## Monostate Singleton Pattern

The theory behind the Singleton pattern is that there should be only one object of a class. Alex Martelli of _Python in a Nutshell_ states that what developers really want is instances sharing the same state. That is to say developers should favour state and behaviour over identity. The monostate is also commonly referred to as the Borg pattern.

To achieve the Borg pattern in python we leverage the `__dict__` type. The dictionary is used to store the state of each object in the class. By intentionally assigning a `__shared_state` to all created instances we can create several unique objects that all have common state. Therefore, if we create two instances, `a` and `b`, we get different objects. But, those objects have a shared state, so if we changed a attribute in `a` it will be copied across to `b`. This is because the `__dict__` inside each object is shared across all objects instantiated by that class.

```python
# Example 1.3 Monostate or Borg Pattern

class Borg:
    __shared_state = {}
    def __init__(self):
        self.__dict__ = self.__shared_state

b = Borg()
a = Borg()
b.thing = 'thing'
b.__dict__
>>> {'thing': 'thing'}
a.__dict__
>>> {'thing': 'thing'}
print('memory slots a: {} b: {}'.format(a, b))
>>> memory slots a: <__main__.B object at 0x7f04d2f780b8> b: <__main__.B object at 0x7f04d2f78160>
```

The real power of the Borg pattern is its ability to subclass and share state.

## Metaclasses and Singletons

Whats a metaclass? It is a class of a class meaning that a class is an instance of its metaclass. Using metaclasses allows the developer to alter the way in which classes are typically created, or the attributes and objects within. For example is you have a class named `MyClass`, you can instantiate a new class called `MyKlass`. By doing this we can now reach in and alter the behaviour of `MyClass` in a way that suits the developer.

Everything is an object in python therefore if we define `integer = 5` and call `type(integer)` it will return `<class 'int'>`. But if we call `type(type(integer))` it will return `<class 'type'>`. This means the metaclass (class of its class) of `int` is `type`. 

The type of class created is determined by its metaclass, so if we created `class A`, its the equivalent of stating `A = type(name, bases, dict)`. This is something python does for us under the covers but also allows us to alter any object via metaclasses. 

- `name`: Name of the class
- 'base': The base class
- 'dict': The attribute variable

```python
# Example 1.4 Metaclassing Singletons

class SingletonMetaclass(type):
    _instance = {}
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instance:
            cls._instance[cls] = super(SingletonMetaclass, cls).__call__(
                *args, **kwargs)
        return cls._instance[cls]

class Logger(metaclass=SingletonMetaclass):
    pass

logger1 = Logger()
logger2 = Logger()
print('logger1: {} logger2: {}'.format(logger1, logger2))
>>> logger1: <__main__.Logger object at 0x7f8cdde52320> logger2: <__main__.Logger object at 0x7f8cdde52320>
```

In example 1.4 we have a few things going on. Firstly, `__call__` is utilised, this is a python special method that needs to be called when creating an object from an already existing class. In the above example `__call__` now takes over the instantiation of the class. While this example hasn't shown much reason to use this method of creating a Singleton is can have utility in some cases.

## Drawbacks of Singleton

As Singletons have only one global point of access they can causes issues such as;

- if the attributes of the object are changed by accident the changes will spill across the entire application,
- can have tight coupling and any change to the global data by one class may cause unexpected issues in other classes.

## Conclusion

Singletons aren't well regarded by some python developers but they can be used in some scenario's such as reading in config files, locking, database operations and logging. Its ability to create one and only one object can be very efficiency on system resources. But ultimately python offers some usable alternatives to the Singleton principle such as; using a module (in and of itself is called much like a singleton within pythons subsystem) and passing the instance to objects that need it via dependency injection.

