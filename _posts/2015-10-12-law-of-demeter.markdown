---
layout: post
title: "Law of Demeter"
date: 2015-10-12 20:25:40
categories: Ruby Rails Refactoring
---

I looked through some past projects and found this masterpiece of a method I wrote about this time last year:

![Awesome Code](/assets/images/demeter.png)

Are you cringing and are your eyes twitching yet? Yeah, mine are. So, yeah, while there are multiple errors in this code (random use of an instance variable, for example), the error that stands out the most is the multiple violations of the law of demeter (LoD).

To atone on my past sins, lets break down the law of demeter and how adhering to it could have helped me out a little in this case.

##Law of Demeter

In short, the law of demeter states that an object should only interact with its close friends. Specifically, the law states: 

> A method of an object should invoke only the methods of the following kinds of objects:
>
> * itself
> * its parameters
> * any objects it creates/instantiates
> * its direct component objects 

So let's look at examples of each law-abiding scenario.

####Itself

{% highlight ruby %}
class Address
  attr_reader :city, :state
  
  def full_address
    "#{city}, #{state}"
  end
end
{% endhighlight %}

This is a simple example of an Address object only invoking methods known to itself. No law breaking here, keep walking officer.

####Its Parameters

{% highlight ruby %}
class JobPost
  def post_url(id)
    "http://www.rubyjobs.com/posts/#{id}"
  end
end
{% endhighlight %}

In this example we have a post_url method that takes an id parameter. Any method parameters are free game to use within the method itself and do not violate the LoD.

####Any Objects it Creates/Instantiates

{% highlight ruby %}
class Mailer
  def prepare_emails(list)
    email_sanitizer = EmailSanitizer.new
    email_sanitizer.sanitize(list)
  end
end
{% endhighlight %}

In this example we are building the email sanitizer object within the prepare_emails method. Creating something means we own it, we can call methods on it without breaking the LoD.

####Its Direct Component Objects

{% highlight ruby %}
class Mailer
  def initialize(email_sanitizer, list)
    @email_sanitizer = EmailSanitizer.new
    @list = list
  end

  def prepare_emails(list)
    @email_sanitizer.sanitize(list)
  end
end
{% endhighlight %}

Similar to the last example, instantiating EmailSanitizer in another method gives us free reign to use it within other class methods. Demeter would be proud in this example.

##Why Demeter

By following the Law of Demeter, all of the above code examples have easy to manage dependencies. LoD helps restrict how deeply a method can reach into another object's interface, preventing tightly coupled code and opening the door to future, headache free changes to the code base.

Let's look at examples where we violate LoD and their consequences to the codebase. 

####Multiple Dots

{% highlight ruby %}
class Mailer
  def send_mailer(order)
    mail(to: order.customer.email, subject: "Thanks for purchasing!")
  end
end
{% endhighlight %}

While my first question is why the send_mailer method takes order as an argument as opposed to the customer directly, it still stands that this method violates the LoD. A tell tale sign for a LoD violation is the presence of multiple dots in a method. In this example our program is initiating a conversation with Order and it's Customer but in reality our program could care less about Order and is really just there to ask Customer about his email address. 

Order has lost control of his dependencies and they are instead performing actions behind it's back. As the chain of custody increases, the potential for an error or the wrong data being returned also increases.

The problem with Rails is that ActiveRecord makes it *really* easy to chain methods together in order to return what we want. Hence my very first example. 

Let's take a moment to look at a variation of a multiple dots scenario:

####Multiple Assignments

{% highlight ruby %}
class Mailer
  def send_mailer(order)
    customer_email = customer.email
    mail(to: order.customer_email, subject: "Thanks for purchasing!")
  end
end
{% endhighlight %}

If you look at the above code briefly you may judge it as not violating the LoD simply because there isn't the tell tale sign of multiple dots. But take a little closer look and you'll notice that we didn't really do anything but shuffle the logic around into a variable. The consequences for violating LoD still apply and our code is left none the wiser. 

So let's fix this.

####Delegation

{% highlight ruby %}
class Mailer
  def send_mailer(order)
    order.send_mailer
  end
end

class Order
  def send_mailer
    mail(to: customer.email, subject: "Thanks for purchasing!")
  end
end
{% endhighlight %}

In this example, we push the behavior of sending mailers to the Order class. The Mailer class can now simply tell an Order to send a mailer rather than asking Customer for information in order to do it itself. The Order is left to perform it's behavior on it's own, under it's own terms. The send_mailer method still results in the same behavior being performed (a mailer is sent to the customer), however, Mailer is no longer reaching through Order to acquire knowledge (knowlege it shouldn't really know).

So that's it for now. A simple solution to solving the most obvious of LoD violations. The question is, however, is this truly ideal and are there better ways to fixing LoD than simple delegation? I'll touch on that in a future post. 



