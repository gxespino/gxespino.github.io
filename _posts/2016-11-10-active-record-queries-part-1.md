---
layout: post
title:  Optimizing ActiveRecord & SQL Queries - Part 1
date:   2016-11-10 08:08:17
categories: Ruby ActiveRecord
---

When I first started learning Rails, I leaned pretty heavily on Ruby's `Enumerable` methods to form the queries I needed. This is OK when you're just starting and arguably if you're just trying to get a small project out the door. But, eventually you'll find yourself in a situation where response time matters and getting good at optimizing queries becomes really important.

I plan to write 4-5 posts on this topic but we can start by looking at how we can optimize Rails' `belongs_to` association queries.

### Domain model

In one of my freelance Rails projects I had a domain model as such:

```ruby
class Customer < ActiveRecord::Base
  belongs_to :tier
end

class Tier < ActiveRecord::Base
  has_many :customers
end
```

### Customer who belonged to a non free tier

The client wanted a way to e-mail all of the _customers who belonged to a non free tier_ (all the paying customers). Easily enough, this could be achieved with the following:

```ruby
Customer.all.select { |customer| customer.tier.paid? }
```

So, we're done, let's push to production? Well not so fast. Let's analyze this query a little bit in Rails console to see what it's really doing to our database.

```
2.3.0 :016 > Customer.all.select { |customer| customer.tier.paid? }
  Customer Load (1.6ms)  SELECT "customers".* FROM "customers"
  Tier Load (0.1ms)  SELECT  "tiers".* FROM "tiers" WHERE "tiers"."id" = ? LIMIT 1  [["id", 1]]
  Tier Load (0.0ms)  SELECT  "tiers".* FROM "tiers" WHERE "tiers"."id" = ? LIMIT 1  [["id", 1]]
  Tier Load (0.0ms)  SELECT  "tiers".* FROM "tiers" WHERE "tiers"."id" = ? LIMIT 1  [["id", 1]]
  Tier Load (0.0ms)  SELECT  "tiers".* FROM "tiers" WHERE "tiers"."id" = ? LIMIT 1  [["id", 1]]
  Tier Load (0.0ms)  SELECT  "tiers".* FROM "tiers" WHERE "tiers"."id" = ? LIMIT 1  [["id", 1]]
  Tier Load (0.0ms)  SELECT  "tiers".* FROM "tiers" WHERE "tiers"."id" = ? LIMIT 1  [["id", 1]]
  ...redacted...
```

### N+1 Anyone?

Looks like we have a classic N+1 query in which for every Customer we're making a separate request to the Tiers table. You can only imagine how inefficient this would be for a mid sized company with thousands or millions of Customers.

What's worse is that we don't even need any actual data from the Tiers table as our final result should just be a list of Customers. With each query, _our database is sending back every one of these Tiers to the application where it is then built out into a full fledged ActiveRecord object_, unnecessarily.
