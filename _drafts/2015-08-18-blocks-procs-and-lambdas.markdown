---
layout: post
title:  "Blocks, Procs & Lambdas"
date:   2015-08-18 01:08:17
categories: Ruby Array Methods
---

Applying for a Ruby/Rails job? You're gonna get asked this. Let's get them straight once and for all.

###What are Blocks, Procs, and Lambdas?###

They are all *closures* or in plain talk: ways of grouping code.

{% highlight ruby %}
# Block

[1, 2, 3].each { |x| puts x + 2 }    # Blocks can be in curly braces 

# 3
# 4
# 5
#  => [1, 2, 3] 

[1, 2, 3].each do                    # Blocks can also be placed inbetween a do/end
  puts x + 2 
end

# 3
# 4
# 5
#  => [1, 2, 3]

# Proc

my_proc = Proc.new { |x| puts x }    # Procs must be instantiated

[1, 2, 3].each(&my_proc)             # Procs can be called as a block by adding an ampersand 

# 1
# 2
# 3
#  => [1, 2, 3]

my_proc.call("HELLO")               

# HELLO
#  => nil

{% endhighlight %}
