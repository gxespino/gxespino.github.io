---
layout: post
title:  "Don't Use Floats for Prices"
date:   2015-09-10 14:20:27
categories: Ruby Design Patterns Data Types
---

I recently learned the hard way the importance of avoiding the use of Floats for storing currency values in Ruby. Floats abide by 'floating point arithmetic' and thus results in the representation of real numbers via a formulaic expressions, namely to serve as a tradeoff between range in precision. More info about the topic can be read [here](https://en.wikipedia.org/wiki/Floating_point).

What this meant was that storing prices in floats often led to an accumulation of small values that overtime will result in inaccuracies. Fore example: 

{% highlight ruby %}
"1.03".to_f - ".42".to_f

#=> 0.61000000000000000001 
{% endhighlight %}

A better option would be to use `Decimal` when using prices. What I've commonly seen used has been `add_column :table, :price, :decimal, :precision => 8, :scale => 2`.


