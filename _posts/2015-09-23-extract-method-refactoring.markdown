---
layout: post
title: "Extract Method Refactoring"
date: 2015-09-23 13:05:40
categories: Ruby Refactoring
---

Sometimes you have complicated expressions that could benefit from being broken down to more manageable and easier to understand chunks of code. 

An easy refactor is to extract out logical bits of the complicated expression into separate methods (consider private methods for these).

Here's an example using a Salary class that calculates an employee's total compensation: 

{% highlight ruby %}
class Salary

  def initialize(base, bonus_percentage: 0, seniority_factor: 0)
    @base = base
    @bonus_percentage = bonus_percentage
    @seniority_factor = seniority_factor
  end

  def total_compensation
    base + (base * (bonus_percentage / 100.0)) + (base * (seniority_factor / 100.0)) 
  end

  private
  
  attr_reader :base, :bonus_percentage, :seniority_factor
end
{% endhighlight %}

The total_compensation method is hard to understand and would benefit from extracting some of the expression into separate methods. 

{% highlight ruby %}
class Salary

  def initialize(base, bonus_percentage: 0, seniority_factor: 0)
    @base = base
    @bonus_percentage = bonus_percentage
    @seniority_factor = seniority_factor
  end

  def total_compensation
    base + bonus + seniority
  end

  private
  
  def bonus
    base * (bonus_percentage / 100.0)
  end

  def seniority
    base * (seniority_factor / 100.0)
  end
  
  attr_reader :base, :bonus_percentage, :seniority_factor
end
{% endhighlight %}

After extracting the expression logic into separate methods, we now have more manageable code that is much easier to understand. If we had tests to test the total_compensation method prior to the refactoring, they should still pass at this point.

**Bonus:** There's a repeating pattern here that would also benefit from refactoring. Let me know what you think in the comments. 
