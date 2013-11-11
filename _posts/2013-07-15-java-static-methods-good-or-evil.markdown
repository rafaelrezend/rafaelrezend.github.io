---
layout: post
title: Java Static methods&#58; Good or evil&#63;
---

I confess: sometimes I am too addicted to static methods. I see them as *third-party service providers*, so that one can simply invoke it with some parameters to be manipulated, and obtain the result without real need to know what is happening behind it. They have their own business.
It's just like invoking a repairman to fix your car, or a washing machine to clean your clothes.
These instances are there, you don't need to re-create them every time.
However, why not implement a self-repair method on your car, or self-cleaning on your clothes?

In fact, there is a growing discussion among Java developers regarding whether a static declaration is reasonable or not, especially in face of growing systems' complexity.
Static methods are global features, they do not belong to any object.
Instead, they are methods of the class only and are not supposed to change the state of the respective instance (in Eclipse: "Cannot make a static reference to the non-static field" error).

Static methods are widely used in utility classes, such as [Math](http://docs.oracle.com/javase/7/docs/api/java/lang/Math.html). What makes this pattern so tempting is the possibility to invoke a method as simply as

```java
MyUtilityClass.handleObjects(objA, objB);
```

instead of

```java
MyUtilityClass muc = new MyUtilityClass();
muc.handleObjects(objA, objB);
```

But then assume **objA** and **objB** are instances of class `MySuperClass` and you want to create `MySubClass` **objC** and **objD**, in which `MySubClass` extends `MySuperClass`.
You still can call `MyUtilityClass.handleObjects(objC, objD)`.
However, if `MySubClass` objects should be handled in a different way, you realize that the logic behind the static `handleObjects(...)` method should actually belong to `MySuperClass` instead.

Furthermore, keeping this method static is no longer reasonable because static methods cannot be overridden in `MySubClass`. Any attempt to do so will actually hide the original method from the parent class and create a new one in the child.
So, your IDE will allow you to *"override"* the static method, but it will lead to a critical consequence, discussed [here](http://www.xyzws.com/Javafaq/can-static-methods-be-overridden/1).
The same issue is known to affect JUnit testing, besides the fact that the testing environment does not have control over the static context.

Finally, the best implementation for this specific case would be then an object method in `MySuperClass` (with the advantage of cutting off a parameter to use `this.` instead).
The invocation would be

```java
objA.handleObject(objB);
objC.handleObject(objD);
```

with `handleObject(...) performing specific tasks depending on the owner object.

**So, when is it reasonable to use static methods?**

[Math](http://docs.oracle.com/javase/7/docs/api/java/lang/Math.html) is one good example. Methods handle primitive data types (which don't hold methods themselves) and don't require internal states. The operations are atomic and well-defined (sin, logarithm, round...).

Additionally, [factory methods](http://en.wikipedia.org/wiki/Factory_(software_concept)) are usually implemented as static (but I would recommend further research about alternatives), as well as utilities that do not affect the logic or behaviour of the application, such as loggers.

Some people claim that static methods are consequences of bad design. Some claim they go against OOP principles, in which objects are supposed to work as an independent *machine*, able to hold and process its own information and communicate with other objects.

Personally, I think there are no relations between their usage and the quality of the design. A great design may have static declarations, in the same way that their absence does not necessarily increase the quality of the final application.
