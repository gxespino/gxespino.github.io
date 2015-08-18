---
layout: post
title:  "Design Patterns in Ruby - Template Method"
date:   2015-08-17 14:28:17
categories: Ruby Design Patterns
---

Sometimes when designing applications you'll come across a section that is constantly changing or being added on to. A tell tale sign of this is when you have a proliferation of if/then statements in a method that all follow the same general structure but for specific cases. That last sentence may not have made too much sense but here are a few examples:

* You have a payment system that takes in customer data and card info but you need specific steps for when a customer uses paypal vs stripe vs dwolla.
* You're sending out an automated report but need to display the output using a variety of file types (html, json, xml).

The template method pattern is utilized by creating a base template (in Ruby's case a parent class) and creating subclasses for differing scenarios that follow the base template.

##Here's an example##

{% highlight ruby %}
# An abstract class with methods that are common
# to it's subclasses. In this case we are creating
# a Game class with the subclasses of Monopoly and
# Chess, of which both share similar methods.

class Game
  def start_game
    setup_board
    make_play(player)
    end_of_game
    display_winner
  end
end

# Now we can create Monopoly and Chess subclasses
# that inherit from Game. 

class Monopoly < Game
  def setup_board
    # initializes Monopoly specific actions
  end

  def make_play(player)
    # for monopoly, may include
    # roll dice, draw card, move piece
  end

  def end_of_game
    # return true if game is over
    # follows specific rules of Monopoly
  end

  def display_winner
    # display who won and how much money
    # they won with
  end
end

# Chess will follow the same template but will have
# its own, specific actions within the methods

class Chess < Game
  def setup_board
    # for chess, may include 
    # setting up the pieces
  end

  def make_play(player)
    # each player takes a turn
    # starts a timer, returnes falses
    # and loops if play is illegal
  end

  def end_of_game
    # returns true if checkmate condition
    # is met
  end

  def display_winner
    # displays who won
  end
end

# Now we can instantiate each game as follows:

monopoly = Monopoly.new
monopoly.start_game

chess = Chess.new
chess.start_game

{% endhighlight %}

As you can see the huge benefit of the template method pattern is the avoidance of huge if/then or case statements within the base class. The template method also leaves us open to implement more games in the future, granted they loosely follow the same basic setup as defined in our template. 

Basically, the template method pattern allows you to account for variations in your algorithms. The template class holds the skeleton of the algorithm while the subclasses perform their individual details.

If you know of an interesting example of the template method pattern being used in the wild, please comment below! 
