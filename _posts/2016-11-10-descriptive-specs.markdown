---
layout: post
title:  "LOLOL"
date:   2016-11-10 08:08:17
categories: Ruby
---

RSpec doesn't always have the best error feedback. Oftentimes the message you receive is too verbose. I notice this a lot when dealing with ActiveRecord models. For example, let's say we're writing a new feature that allows us to sort a resource via an uploaded_on attribute.

Our spec may resemble:

```ruby
context '.recently_uploaded_first' do
  it 'sorts the collection so that recently uploaded pics are first' do
    new = create(:picture, uploaded_on: Date.today)
    old = create(:picture, uploaded_on: 2.days.ago)
    middle = create(:picture, uploaded_on: Date.yesterday)

    expect(Picture.recently_uploaded_first).to eq [new, middle, old]
  end
end
```

Run this test prior to implementing the solution and RSpec will vomit an error message that fails to help us now let alone four months later when we have even less context.

{% highlight vim %}
Failures:

  1) Picture.recently_uploaded_first sorts the collection so that recently uploaded pics are first
     Failure/Error: expect(Picture.recently_uploaded_first).to eq [new, middle, old]

       expected: [#<Picture id: 1, description: "old", created_at: "2015-12-08 00:23:34", updated_at: "2015-12-10 00:23:34", uploaded_on: "2015-12-08 00:23:34", file_type: "jpeg", size: "5123", type: "Thumbnail">, #<Picture id: 2, description: "old", created_at: "2015-12-08 00:23:34", updated_at: "2015-12-10 00:23:34", uploaded_on: "2015-12-08 00:23:34", file_type: "jpeg", size: "5123", type: "Thumbnail">, #<Picture id: 3, description: "old", created_at: "2015-12-08 00:23:34", updated_at: "2015-12-10 00:23:34", uploaded_on: "2015-12-08 00:23:34", file_type: "jpeg", size: "5123", type: "Thumbnail">]
            got: [#<Picture id: 2, description: "old", created_at: "2015-12-08 00:23:34", updated_at: "2015-12-10 00:23:34", uploaded_on: "2015-12-08 00:23:34", file_type: "jpeg", size: "5123", type: "Thumbnail">, #<Picture id: 1, description: "old", created_at: "2015-12-08 00:23:34", updated_at: "2015-12-10 00:23:34", uploaded_on: "2015-12-08 00:23:34", file_type: "jpeg", size: "5123", type: "Thumbnail">, #<Picture id: 3, description: "old", created_at: "2015-12-08 00:23:34", updated_at: "2015-12-10 00:23:34", uploaded_on: "2015-12-08 00:23:34", file_type: "jpeg", size: "5123", type: "Thumbnail">]
 
{% endhighlight %}

So, let's fix this and see if we can improve the readibility of our specs. 

```ruby
context '.recently_uploaded_first' do
  it 'sorts the collection so that recently uploaded pics are first' do
    create(:picture, uploaded_on: Date.today, description: "new")
    create(:picture, uploaded_on: 2.days.ago, description: "old")
    create(:picture, uploaded_on: Date.yesterday, description: "middle")

    expect(Picture.recently_uploaded_first.map(&:description)).to eq ["new", "middle", "old"]
  end
end
```

So when we run our test again we now get this: 

{% highlight vim %}
Failures:

  1) Picture.recently_uploaded_first sorts the collection so that recently uploaded pics are first
     Failure/Error: expect(Picture.recently_uploaded_first.map(&:description)).to eq ["new", "middle", "old"]

       expected: ["new", "middle", "old"]
            got: ["middle", "new", "old"]
{% endhighlight %}

Ah, much better. We know instantly that we have a sorting problem and if our implementation were to erroneously change in the future, we'll know instantly what the problem is. 

Thoughts?
