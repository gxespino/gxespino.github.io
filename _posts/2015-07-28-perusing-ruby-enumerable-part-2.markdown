---
layout: post
title:  "Perusing Ruby Enumerable - Part 2"
date:   2015-07-28 10:50:15
categories: ruby enumerable 
---

1. **find_all / select** like find/detect but does not stop at the first true value, returns all the true values in an array. 

2. **find_index** acts just liked find, finds the first value that is non-false, but instead of returning the value, it returns the value's index. `(1..100).find_index(50) #=> 49`

3. **first** returns the first element or first(n) elements in an array. Returns nil if there is none. Gotcha: Returns an array. So if you use this on a hash, know that you are getting an array pairing of the key/value.

4. **flat_map** returns a new array with the results of running block once for each element in enum.

5. **grep** results in an array of elements where the supplied Pattern === to element. Patterns usually are regexp but can also be classess/modules, and a range. You can add a block after the grep and each passing element will be passed through it. 

6. **group_by** Groups the collection by the result of the supplied block. Can be immediately useful when grouping collectiosn by months or days in Rails apps. There are a lot of uses for this. Just iterate through the supplied hash using each to display grouped data in a Rails view. 

