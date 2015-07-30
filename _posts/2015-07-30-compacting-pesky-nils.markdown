---
layout: post
title:  "Compacting Pesky Nils"
date:   2015-07-30 10:09:15
categories: ruby array
---

Nils will always find a way to sneak into your code. Given an array of values, any of which may be nil (from user input or unexpected method returns among others), use `Array#compact` to remove all nil elements. 

Heres an example of what could happen: 

{% highlight ruby %}
first = nil
middle = nil
last = "Forgetfull"

full_name = [first, middle, last].join(" ")
=> "  Forgetfull"
{% endhighlight %}

Using compact will remove the unexepected spaces:

{% highlight ruby %}
first = nil
middle = nil
last = "Forgetfull"

full_name = [first, middle, last].compact.join(" ")
=> "Forgetfull"
{% endhighlight %}

At the end of the day it's best to assume any value could potentially be nil - so it's best to code in ways that prevents nil (either through coercion or removal) or risk that pesky `NoMethodError`. 
