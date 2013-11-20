---
layout: post
title: The tricks of the &#43;&#61; operator
---

Suppose you have the two variables below:

```java
int x = 10;
long y = 20;
```

Making `x = x + y` does not work. But `x += y` work. Why?

It work because, according to the [Java Language Specification](http://docs.oracle.com/javase/specs/jls/se5.0/html/expressions.html#15.26.2), *"A compound assignment expression of the form E1 op= E2 is equivalent to E1 = (T)((E1) op (E2)), where T is the type of E1, except that E1 is evaluated only once."*

So, following the example of the specification:

```java
short x = 3;
x += 4.6;
```

Results in x having the value 7 because it is equivalent to:

```java
short x = 3;
x = (short)(x + 4.6);
```

Why is it important to know? Most of times we use the operators `+=`,`*=` etc just to shorten the line and avoid redundancy. It makes the code cleaner.
However, it is important to have in mind an unexpected loss of precision when such operations are performed between two different types. That may even happen by accident and no compiler is going to prevent you of doing so.