---
layout: post
title:  "Smart Array Arguments"
date:   2015-10-21 12:08:17
categories: Ruby
---

I'm creating an application that ranks poker hands. To do this I've created a domain object called the `PokerHandValue` that consists of the following attributes: `hand_rank`, `used_cards`, `kickers`. The used_cards and kickers variables are arrays consisting of `Card` objects. 

Sometimes a made Hand consists of just a high card and sometimes all five cards are used. Sometimes the kickers array can have zero cards. The point is, the `PokerHandValue` needs to account for the possibility of being passed a single object, an array, or even `nil`. 

Consider for a moment:

```ruby
class PokerHandValue
  def initialize(hand_rank, used_cards, kickers)
    @hand_rank  = hand_rank
    @used_cards = used_cards
    @kickers    = kickers
  end

  def hand
    @used_cards + @kickers
  end
end
```

The way I've structured PokerHandValue, it is expecting both used_cards and kickers to be arrays. If we somehow pass PokerHandValue nil, we'll get a TypeError when trying to call the hand method. 

To avoid this, we can utilize a little known method: Array. 

```ruby
class PokerHandValue
  def initialize(hand_rank, used_cards, kickers)
    @hand_rank  = hand_rank
    @used_cards = Array(used_cards)
    @kickers    = Array(kickers)
  end

  def hand
    @used_cards + @kickers
  end
end
```

Simple and elegant! Now our used_cards and kickers will always return an array no matter what sort of object sneaks into our arguments.

So let's break down exactly what the Array method does for us:

```ruby
irb> Array(nil)
---> []

irb> Array([])
---> []

irb> Array(%w(Ace Ten King))
---> ["Ace", "Ten", "King"]
```

With such a small change in our code, we increase it's flexibility, and reduce the potential of bugs. That's a great win with few, if any, side effects. 

