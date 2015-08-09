---
layout: post
title:  "Performance Optimization for Zero Users"
date:   2015-08-08 11:08:17
categories: Ruby Design Optimization
---

A common trap that I've found myself in as I gain more experience and delve further into application design and TDD is the paralysis analysis that comes from pre optimizing for users that I don't have yet. 

Case in point: Earlier this week I struggled over the best way to test semi-static objects that will eventually be created from a CSV file (a list of states and counties). I did my usual googling and reading half a dozen blogs regarding the issue and a few options emerged from my research:

1. Create a location object factory using FactoryGirl
2. Seed the test database, test the seeder, and then test those objects
3. Use a fixture

I pondered on these options for quite a while. I even asked Reddit and posed the question in a couple of Ruby slack groups. The pre optimizer in me wanted to go for option two, which I'll probably end up implementing later down the road as my tests become unwieldly. But, the fastest, and simplest test to implement right at that moment was either option one or two. At the end of the day I opted to go with FactoryGirl and this allowed me to move past the Location model specs and onto another problem.

At the end of the day, after spending a couple hours pondering on my options (two car rides, a couple cups of coffee, and two half hour conversations with more experienced Rubyists) the actual implementation of the tests and code was < 20 minutes. Had I not been so bent on pre optimizing, I could have knocked out a basic & simple iteration of the code and moved on much earlier. 

One of my mentors said it best: "Don't pre optimize for performance, especially for an app with ZERO users." 

--End Ramble 
