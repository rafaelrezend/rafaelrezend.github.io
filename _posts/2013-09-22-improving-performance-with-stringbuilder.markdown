---
layout: post
title: Improving performance with StringBuilder
---

Let's say we have a `String content` and we want to concatenated a `String line` to it.
Nothing more natural than doing:

{% highlight java %}
content = content + line;
{% endhighlight %}

Or simply

{% highlight java %}
content += line;
{% endhighlight %}

It works! Simple and obvious. However, **Java Strings are [immutable](http://en.wikipedia.org/wiki/Immutable_object)**. It means that every re-assignment above creates a new instance of String on memory with the content of the previous string plus the concatenated one.

This simple solution works, but what if the String is used within a loop to concatenate hundred/thousand lines from a buffer?

{% highlight java %}
// Holds the String content
String content = "";

String line = "";
while ((line = ContentBuffer.readLine()) != null)
	content += line;
{% endhighlight %}

The implementation above can impact the performance as a new instance is created for every new line of text.
So, a solution could be to use a `StringBuilder` instead:

{% highlight java %}
// Holds the String content
StringBuilder contentSB = "";

String line = "";
while ((line = ContentBuffer.readLine()) != null)
	contentSB.append(line);

String content = contentSB.toString();
{% endhighlight %}

`StringBuilder` is mutable, so it can be changed without new instantiation.
That's it!!

An interesting discussion can be found [here](http://www.javaranch.com/journal/2003/04/immutable.htm) and [here](http://programmers.stackexchange.com/questions/195099/why-is-string-immutable-in-java).
