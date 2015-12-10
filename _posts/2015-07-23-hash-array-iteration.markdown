---
layout: post
title:  "Hash Array Iteration"
date:   2015-07-23 17:05:15
categories: update
---

Here's a tricky one:

{% highlight ruby %}
hash_array = [{"pears" => 1}, {"pears" => 4}, {"apples" => 4}, {"oranges" => 7}]
}]
{% endhighlight %}

If you want to consolidate the hashes with the same key while adding their values, use inject and merge:

{% highlight ruby %}
hash_array.inject do |fruit, amount|
  fruit.merge(amount) { |_x, amount_1, amount_2| amount_1 + amount_2 }
end

#=> prints {"pears"=>5, "apples"=>4, "oranges"=>7}
{% endhighlight %}

Careful though, the result is now a hash and no longer an array. 
