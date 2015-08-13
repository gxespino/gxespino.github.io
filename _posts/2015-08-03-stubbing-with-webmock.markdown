---
layout: post
title:  "Stubbing With WebMock"
date:   2015-08-03 02:18:17
categories: Ruby Rails Testing Mocking Stubs
---

A lot of times your app will require an external source - whether you're building a scraper, hitting an API, or just referencing a webpage.

External sources should be tested in isolation rather than directly. This prevents problems such as hitting API limits, connectivity dependence, and slower test suites amongst other things. 

The [WebMock](https://github.com/bblimke/webmock) gem is great at stubbing out external requests and we can manipulate it to return what is expected. 

**Here's an example test for a browser feature:**

First make sure to install rspec, webmock, and capybara to your gemfile and to require them in your spec_helper.

We'll create a feature spec for browsing a web page:

{% highlight ruby %}
# spec/features/browser_browses_spec.rb
require "rails_helper"

feature "browser" do
  it "browses Matz's wiki" do 
    uri = URI('https://en.wikipedia.org/wiki/Yukihiro_Matsumoto')

    wiki_page = Net::HTTP.get(uri)

    expect(wiki_page).to have_content("MINASWAN")
  end
end
{% endhighlight %}


Running our test should show us a WebMock error:

{% highlight vim %}
Failures:

  1) Browser browses Matz's wiki
       Failure/Error: response = Net::HTTP.get(uri)
         WebMock::NetConnectNotAllowedError:
         Real HTTP connections are disabled. Unregistered request: GET https://en.wikipedia.org/wiki/Yukihiro_Matsumoto with headers {'Accept'=>'*/*', 'Accept-Encoding'=>'gzip;q=1.0,deflate;q=0.6,identity;q=0.3', 'Host'=>'en.wikipedia.org', 'User-Agent'=>'Ruby'}

         You can stub this request with the following snippet:

         stub_request(:get, "https://en.wikipedia.org/wiki/Yukihiro_Matsumoto").
           with(:headers => {'Accept'=>'*/*', 'Accept-Encoding'=>'gzip;q=1.0,deflate;q=0.6,identity;q=0.3', 'Host'=>'en.wikipedia.org', 'User-Agent'=>'Ruby'}).
           to_return(:status => 200, :body => "", :headers => {})
         

============================================================
Finished in 0.00732 seconds (files took 5.8 seconds to load)

1 example, 1 failure
{% endhighlight %}


WebMock is awesome because it provides us a snippet we can use. Simply copy the above snippet and let's paste it into our spec:

{% highlight ruby %}
# spec/features/browser_browses_spec.rb
require "rails_helper"

feature "browser" do
  it "browses Matz's wiki" do 
    stub_request(:get, "https://en.wikipedia.org/wiki/Yukihiro_Matsumoto").
      with(:headers => {'Accept'=>'*/*', 'Accept-Encoding'=>'gzip;q=1.0,deflate;q=0.6,identity;q=0.3', 'Host'=>'en.wikipedia.org', 'User-Agent'=>'Ruby'}).
      to_return(:status => 200, :body => "", :headers => {})

    uri = URI('https://en.wikipedia.org/wiki/Yukihiro_Matsumoto')

    wiki_page = Net::HTTP.get(uri)

    expect(wiki_page).to have_content("MINASWAN")
  end
end
{% endhighlight %}


Running our specs again should give us this error:

{% highlight vim %}
Failures:

  1) Browser browses Matz's wiki
       Failure/Error: expect(response).to have_content("MINASWAN")
         expected to find text "MINASWAN" in ""
       # ./spec/features/browser_browses_spec.rb:15:in `block (2 levels) in <top (required)>'
       
Finished in 0.02709 seconds (files took 2.65 seconds to load)
1 example, 1 failure
{% endhighlight %}


Great, so WebMock is doing it's job and blocking external requests. Now we have to tell it what to return instead. We can do this by replacing the body with whatever we're requesting, in this case Matz's wiki page.

Make sure you're in the app directory and that you've created the spec/fixtures folder. Then we can  use the terminal to get what we want:

{% highlight bash %}
$ cd spec/fixtures && curl https://en.wikipedia.org/wiki/Yukihiro_Matsumoto > matz.html && cd -
{% endhighlight %}

That command will go into the fixtures directory, paste the html from Matz's wiki into a new file and save it as matz.html, and lastly it'll cd us back into our app's root directory.

Now that we have Matz's wiki page saved, we can reference it in our tests:

{% highlight ruby %}
# spec/features/browser_browses_spec.rb
require "rails_helper"

feature "browser" do
  let(:matz_wiki) { File.read( Rails.root + 'spec/fixtures/matz.html' ) }

  it "browses Matz's wiki" do 
    stub_request(:get, "https://en.wikipedia.org/wiki/Yukihiro_Matsumoto").
      with(:headers => {'Accept'=>'*/*', 'Accept-Encoding'=>'gzip;q=1.0,deflate;q=0.6,identity;q=0.3', 'Host'=>'en.wikipedia.org', 'User-Agent'=>'Ruby'}).
      to_return(:status => 200, :body => matz_wiki, :headers => {})

    uri = URI('https://en.wikipedia.org/wiki/Yukihiro_Matsumoto')

    wiki_page = Net::HTTP.get(uri)

    expect(wiki_page).to have_content("MINASWAN")
  end
end
{% endhighlight %}


And with that our test should pass!

{% highlight vim %}
.

Finished in 0.01472 seconds (files took 2.62 seconds to load)
1 example, 0 failures
{% endhighlight %}


**Things to consider**

1. Our copy of Matz's wiki can get out of sync if anyone were to his wiki in the future. This could later lead to failed tests.
2. In a large application, we'll end up with a lot of copied external sources (html, json, etc). This could lead to a lot of maintenance overhead.

*Thanks to [thoughtbot](https://thoughtbot.com) for the original inspiration on this post.*
