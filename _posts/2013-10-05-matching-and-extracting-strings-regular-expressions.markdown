---
layout: post
title: Cookbook&#58; Matching and Extracting Strings with Regular Expression
---

Regular expressions are very handy! They are most of time the best way to extract or validate a String.
Coming up with the correct expression, though, is usually a bit tricky, but there are thousands of references out there on how to refine them for your criteria.

Personally, I recommend [regexpal](http://regexpal.com/) to test your regular expression before going into code. Easy to use, clean and intuitive.

After the expression is resolved, these are the basic steps to match a String in Java and extract a specific part of it:

{% highlight java %}
// create a pattern
Pattern pattProduct = Pattern.compile(regex, Pattern.DOTALL);
{% endhighlight %}

`regex` is the regular expression gives as String.

`Pattern.DOTALL` means that the dot `.` in the regular expression matches any character, including line terminator.
More info on [Javadoc](http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html).

{% highlight java %}
// create a matcher from the given pattern for the String content
Matcher matcher = pattProduct.matcher(content);
{% endhighlight %}

Basically, the [`Matcher`](http://docs.oracle.com/javase/7/docs/api/java/util/regex/Matcher.html) is trying to match the pattern above with a given String `content`.

Then, one way to know if there is a match is by using the boolean `find()` method, which will look for subsequent matches.

{% highlight java %}
// find the first pattern match
if (!matcher.find()){
	System.out.println("The content does not match the given pattern.");
}
{% endhighlight %}

Then, an advanced usage would be to extract a part comprehended by that pattern.
For instance, suppose we want to extract the value represented by `#VALUE#` in the following URL tag:

{% highlight html %}
<tag style="text-align:left;">#VALUE#</tag>
{% endhighlight %}

The respective regular expression should be:

{% highlight java %}
String regex = "<tag style=\"text-align:left;\">(.*?)</tag>"
{% endhighlight %}

So, we are interested on everything between `<tag style="text-align:left;">` and `</tag>` tags, which is represented by the regular expression `(.*?)`.

To extract this value, the `Matcher` provides the function `Matcher.group()`.
So, given a `content` containing the String `<tag style="text-align:left;">123456789</tag>`:

{% highlight java %}
System.out.println(matcher.groupCount());
System.out.println(matcher.group(1));
{% endhighlight %}

It will print `1` and `123456789` respectively.

As a conclusion, we could see how a regular expression allied to the Java `Pattern` and `Matcher` classes could make life much easier.
