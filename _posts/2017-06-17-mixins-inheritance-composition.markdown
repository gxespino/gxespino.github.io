---
layout: post
title:  "You Should Be Defaulting to Composition"
date:   2017-06-12 10:02:13
categories: Ruby Rails Refactoring
---

Here's the scenario:

Imagine joining a team hard at work on a large codebase. A few days in and you've slowly started to get a grasp for the domain. There seems to be multiple types of users—attorneys, applicants, translators—all of whom do similar actions but in slightly different ways.

Creating a user, for example, will always require serializing and posting to an external API. What differs between user types is the serialization and the API endpoint. Simple enough, right?

Now consider the following code:

```ruby
class Attorney::CreateUserAction < Base::CreateUserAction
  class << self
    private

    def endpoint
      "#{ExternalDBHttpConfig.attorney_save_endpoint}/v1/save"
    end

    def serialize(user_data)
      Attorney::UserSerializer.new(user_data).to_json
    end
  end
end
```

I don't like the above code. Here's why:

1. At initial glance, I'm not 100% sure about what this class can do. I can make a fair assumption that this class is responsible for creating an attorney type user, but how it would go about doing that, I'm not immediately sure.
2. The interface of this object shows two private class methods: `.endpoint` and `.serialize` but no public methods. We're left assuming, again, what the object's behavior and responsibilities are.

Lets look at the base class:

```ruby
class Base::CreateUserAction
  class << self
    def execute(uid, user_data)
      post_body = {
        body: serialize(user_data),
        headers: { 'UserId' => uid, 'Content-Type' => 'application/json' }
      }

      HttpClient::ExternalUserRepository.post(endpoint, body: post_body) do |response|
        Result.new(body: response.body, status: response.status)
      end
    end

    private

    def endpoint
      raise NotImplementedError, 'Override this method in a subclass'
    end

    def serialize(_user_model)
      raise NotImplementedError, 'Override this method in a subclass'
    end
  end
end
```

Ok, so it's starting to make a little more sense now. We have your standard _Template Pattern_, and, knowing that context, I can probably assume that all of the other `CreateUserAction` subclasses will also rely on a public `#execute` method.

Still, I don't like the above code and again, here's why:

1. While the team has succeeded in DRYing up the code, they've done that by coupling all of the `CreateUserAction` subclasses together. Any change to the skeleton algorithm in `.execute` will reverberate down to all of the subclasses, possibly leading to unintended behavior. How many times have you refactored a parent class, had all of its test pass, but somehow broke tests for one of its subclasses?
2. Any future variance to the `.execute` algorithm will inevitably lead to either an explosion of conditionals, additional layers of of inheritance, the introduction of multiple inheritance (modules), or some tangled combination of all three. 

A lot of times I'll hear that the pattern above is _probably_ fine and  a lot better (maybe) than duplicating the execution logic everywhere. Besides, how often can we expect the process of posting to external APIs to change? Not often... right?

In reality, the opposite is most likely true. Whats _probably_ fine today, is a "why did we do it like this?!" tomorrow. So, why not reach for a design that allows for flexibility up front—even when the reasons for it are not clear today?

Consider: 

```ruby
class CreateUserAction
  def execute(uid, user_data, endpoint)
    post_body = {
      body: user_data,
      headers: { 'UserId' => uid, 'Content-Type' => 'application/json' }
    }

    HttpClient::ExternalUserRepository.post(endpoint, body: post_body) do |response|
      Result.new(body: response.body, status: response.status)
    end
  end
end

class CreateAttorney
  def initialize(uid, user_data, action: CreateUserAction, serializer: AttorneySerializer)
    @uid = uid
    @user_data = user_data
    @action = action
    @serializer = serializer
  end

  def execute
    @action.execute(uid, serialized_data, endpoint)
  end

  private

  def endpoint
    "#{ExternalDBHttpConfig.attorney_save_endpoint}/v1/save"
  end

  def serialized_data
    @serializer.new(user_data).to_json
  end
end
```

It wasn't hard to get to the above refactoring and yet I'd argue our project is much more maintainable and extensible with it. `CreateUserAction` is a high level, plain ole' ruby object that is no longer concerned with serializing `user_data`. It builds the `post_body` directly with the `user_data` it's provided with. 

Instead, the serializer object is a injected into `CreateAttorney`, giving us the flexibility to alter that part of the algorithm in the future, without modifying our current app code. Another improvement is that `CreateAttorney`'s functionality and responsibilities are immediately made available when you open up the class. Although, I'm not a huge fan of the `execute` method as it could be more descriptive, we can see that `CreateAttorney` delegates the actual http call execution to whatever action object it is initialized with.

By encapsulating logic into small, separate objects, and then composing those objects, we're better able to deal with future variants in our algorithm. Any future scenario can be met with an endless combination of objects, any future variant can be a new object.

The problem with composition is that, you guessed it, you have to actually _compose_ objects. When taken to the extreme you'll have to manage complicated frameworks and dependency graphs just to perform your application's core functionality. Pretty soon, you'll be combing through numerous layers of indirection just to trace the execution path of a method. But, I think dealing with a composition problem is easier to reason about than having to untangle inheritance hierarchies.
