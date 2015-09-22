---
layout: post
title:  "Extract Value Objects"
date:   2015-09-12 10:05:44
categories: Ruby Design Patterns Data Types OOP 
---

Sometimes while programming you'll come upon objects (or scenarios that would benefit from an object) that are most useful when defined solely by their values and not their identity. These situations call for the use of Value Objects. But what are they exactly and why use them?

Value Objects are objects that are defined solely by their contents. These objects don't change, and are therefore immutable by nature. A better way to explain this is to think of a Product object in an e-commerce application. A Product Object has a certain identity and state that can change overtime. It can, for example, be out of stock, on sale, and could even change it's name entirely yet still be the same object. A Price value Object (possibly nested within a Product Object), however, doesn't change. A Product Object can have different Prices overtime while retaining it's identity. A $5 price will always equate to $5. Changing the attributes of a Price Object results in a different object entirely.

Uses
• Prevent duplicated code from making the same observations of primitive
objects throughout the code base.
• Remove large classes by splitting out query methods associated with a
particular variable.
• Make the code easier to understand by fully encapsulating related logic
into a single class, following the single responsibility principle.
• Eliminate divergent change by extracting code related to an embedded
semantic type.
{% highlight ruby %}
"1.03".to_f - ".42".to_f

#=> 0.61000000000000000001 
{% endhighlight %}

A better option would be to use `Decimal` when using prices. What I've commonly seen used has been `add_column :table, :price, :decimal, :precision => 8, :scale => 2`.


