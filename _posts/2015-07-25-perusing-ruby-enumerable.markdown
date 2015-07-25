---
layout: post
title:  "Perusing Ruby Enumerable - Part 1"
date:   2015-07-25 09:25:15
categories: ruby enumerable 
---

1. all? Returns true or false depending on the results of iterating through the collection. If there is a nil or false in the collection, all? will return false - obviously. If there is no block, ruby adds an implicit { |obj| obj } which returns true. 

2. any? Here's a gotcha - it will return true if ANY of the elements are not faly or nil. 

3. collect - Returns a new array. 

4. collect_concat == flat_map 

5. count - Counts number of items in a collection. Add an argument to count the number of said argument in the collection. If a block, the number of elements that yield true are counted. 

6. cycle - potential for infinity. Takes an argument that defaults to nil (cycle forever). 

7. find - takes an argument that will return it's result if no object matches the enumerable. Gotcha - need to call argument with a lambda otherwie you'll get an error. 

{% highlight ruby %}
(1..10).find( -> { "nope" }) { |num| num == 15 }
=> "nope"
{% endhighlight %}

8. drop/drop while - Drops the n element in a collection. Does exactly what you think it does. 
