---
layout: post
title:  "Extract Value Objects"
date:   2015-09-16 10:05:44
categories: Ruby Design Patterns Data Types OOP 
---

Sometimes while programming you'll come upon objects (or scenarios that would benefit from an object) that are most useful when defined solely by their values and not their identity. These situations call for the use of Value Objects. But what are they exactly and why use them?

Value Objects are objects that are defined solely by their contents. These objects don't change, and are therefore immutable by nature. As an example, consider a Product object in an e-commerce application. A Product Object has a certain identity and state that can change overtime. It can, for example, be out of stock, on sale, and could even change it's name entirely yet still be the same object. A Price value Object (possibly nested within a Product Object), however, doesn't change. A Product Object can have different Prices overtime while retaining it's identity. A $5 price will always equate to $5. Changing the attributes of a Price Object results in a different object entirely.

#####Benefits
+ Prevent code duplication
+ Reduce large classes by splitting out logic associated with a
particular variable
+ Make the code easier to understand by fully encapsulating related logic
into a single class, following the single responsibility principle.

#####Here's an example:


{% highlight ruby %}
# app/lib/report_card.rb

class ReportCard
  attr_accessor :grades

  def initialize(attributes = {})
    @scores = attributes[:scores]
    @grades ||= grade_scores
  end

  private

  def grade_scores
    @scores.map do |score|
      grade_score(score)
    end
  end

  def grade_score(score)
    if score < 60
      'F'
    elsif score < 70
      'D'
    elsif score < 80
      'C'
    elsif score < 90
      'B'
    else
      'A'
    end
  end
end


{% endhighlight %}

You can see that ReportCard is bloated with logic on how to grade scores. We can extract that logic out into a separate Grade object:

{% highlight ruby %}
# app/lib/grade.rb

class Grade
  attr_reader :score
  
  def initialize(score)
    @score = score
  end

  def letter
    grade_score(score)
  end

  private

  def grade_score(score)
    if score < 60
      'F'
    elsif score < 70
      'D'
    elsif score < 80
      'C'
    elsif score < 90
      'B'
    else
      'A'
    end
  end
end

# app/lib/report_card.rb

class ReportCard
  attr_accessor :grades
  
  def initialize(attributes = {})
    @scores = attributes[:scores]
    @grades ||= grade_scores
  end

  private

  def grade_scores
    @scores.map do |score|
      Grade.new(score).letter
    end
  end
end

{% endhighlight %}

By extracting out the logic of grading a score into the Grade class, our code is now easier to understand and adheres to the single responsibility principle that we Rubyists strive for. More on the topic of value objects by [Dirk Riehle](http://dirkriehle.com/computer-science/research/1998/ubilab-tr-1998-10-1.html).
