---
layout: post
title:  "Perusing Ruby Enumerable - Part 1"
date:   2015-07-25 09:25:15
categories: ruby enumerable 
---

1. **all?** Returns true or false depending on the results of iterating through the collection. If there is a nil or false in the collection, all? will return false - obviously. If there is no block, ruby adds an implicit `{ |obj| obj }` which returns true. 

2. **any?** Here's a gotcha - it will return true if ANY of the elements are not faly or nil. 

3. **collect** - Returns a new array. 

4. **collect_concat** == flat_map 

5. **count** - Counts number of items in a collection. Add an argument to count the number of said argument in the collection. If a block, the number of elements that yield true are counted. 

6. **cycle** - potential for infinity. Takes an argument that defaults to nil (cycle forever). 

7. **find** - takes an argument that will return it's result if no object matches the enumerable. Gotcha - need to call argument with a lambda otherwie you'll get an error. 

{% highlight ruby %}
(1..10).find( -> { "nope" }) { |num| num == 15 }
=> "nope"
{% endhighlight %}

8. **drop/drop** while - Drops the n element in a collection. Does exactly what you think it does. 

9. **each_cons** - returns a consecutive `n` of elements. Weird one here, can't think of any uses for it, yet. 

10. **each_slice** - immediately seems useful. It iterates through the collection and returns an array (or slice rather) of n elements. 

11. **each_with_index** - So this enumerable allows you to write a block that iterates through the element indexes in your collection. There is potential business use for this, I can already tell. 

12. **each_with_object** - Seems like a better way to create hashes as opposed to inject. Will have to play with this one. 

