---
layout: post
title:  "First vs Take - Subtle Nuances"
date:   2015-08-13 01:08:17
categories: Ruby Array Methods
---

There are subtle differences between the #first and #take methods for Ruby's array collection. In most cases, they output the same result. See below:

{% highlight ruby %}
array = [1,2,3,4,5,6,7,8,9]

array.first(3) 

#=> [1,2,3]

array.take(3)

#=> [1,2,3]
{% endhighlight %}

What will get you in trouble, though, is when you are dealing with empty arrays. #take will throw an ArgumentError anytime you don't provide any arguments. #first, however, will return nil if you don't provide an argument. See below:

{% highlight ruby %}
empty_array = []

empty_array.first 

#=> ArgumentError: Wrong number of arguments (0 for 1)

empty_array.first

#=> nil
{% endhighlight %}

What does this mean? Well, in most cases, I would want to consider using #take over #first as it's much easier (IMO) to debug an ArgumentError over a NoMethodError due to something being nil. It's also easier to remember ahead of time to always call the #take method with an argument, as opposed to remembering whether or not the array you're working on is empty or not, again IMO. In dynamic data, in which the possibility of an empty array is real, using #take would prevent the pesky NoMethodError that you'd get from unknowingly returning a nil.
