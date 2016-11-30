---
layout: post
title:  "Optimizing ActiveRecord & SQL Queries - Part 1"
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

### Customers belonging to a free tier

The client wanted a way to e-mail all of the __customers belonging to a free tier__ (probably to guilt them into paying). Easily enough, this could be achieved with the following:

```ruby
Customer.all.select { |customer| customer.tier.freemium }
```

So, we're done, let's push to production? Well not so fast. Let's analyze this query a little bit in Rails console to see what it's really doing to our database.

```
2.3.0 :016 > Customer.all.select { |customer| customer.tier.freemium }
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

What's worse is that we don't even need any actual data from the Tiers table as our final result should just be a list of Customers. With each query, __our database is sending back every one of these Tiers to the application where it is then built out into a full fledged ActiveRecord object__, unnecessarily.

### What SQL query do we really want anyway?

If you're familiar with SQL at all, sometimes it's easier to write up the optimized query that you want and to back your way into an ActiveRecord query (or not if it's a really complex query). In our case the SQL query we want is:

```
SELECT *
FROM customers
INNER JOIN tiers
  ON tiers.id = customer.tier_id
WHERE tiers.paid = false
```

### ActiveRecord's `#join`

The above query should give us everything we need in one database call. The main players are the JOIN and WHERE statements. Fortunately, ActiveRecord has convenience methods for these SQL statements. So, lets break this down one by one starting with the join clause.

```ruby
Customer.all.joins(:tier)
```

That was easy. If we check this out on the console we should more or less get something like:

```
SELECT *
FROM customers
INNER JOIN tiers
  ON tiers.id = customer.tier_id
```

Great, so the next step is to simply implement the WHERE clause:

```ruby
Customer.all.joins(:tier).where(tiers: { paid: false })
```

And, if we plug this into Rails console we'll see that we're right back to the SQL query we were aiming for.

### Refactor with `#merge`

At this point it would probably be OK if we committed this code and pushed it up. But, we can make it just a little bit better by separating concerns between what a Customer and Tier should be doing. Let's start by ensuring that Tiers are solely responsible for knowing whether or not it is free or paid:

```ruby
class Tier < ActiveRecord::Base
  def self.freemium
    where(paid: false)
  end
end
```

Now, in the Customer model we can simply __tell__ Tier what to do and not __ask__ it about itself. Our complete solution is now:

```ruby
class Customer < ActiveRecord::Base
  def self.not_paying
    joins(:tier).merge(Tier.freemium)
  end
end
```

And as a last check, we can try this out in the console one more time to ensure we're getting the same SQL from before--which it does!

```
2.3.0 :016 > Customer.not_paying
 Customer Load (0.5ms)  SELECT "customers".* FROM "customers" INNER JOIN "tiers" ON "tiers"."id" = "customers"."tier_id" WHERE "tiers"."paid" = 'f'
```
