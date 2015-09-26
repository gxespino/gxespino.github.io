---
layout: post
title: "Specs at a Single Level of Abstraction"
date: 2015-09-23 13:05:40
categories: Ruby Refactoring
---

Every intro to testing tutorial out there will have you write tests at multiple levels of abstraction. With larger test suites, this can get quite unwieldy as your readers are forced to abstract on the fly. As Ruby devs, it's our mission to increase readibility and conciseness at every turn. 

**Multiple levels of abstraction**

Here's an original spec file I had for a Rails app I'm building. Context: this is testing the dataset creation feature in which a dataset requires a name, and two separate files. 

{% highlight ruby %}
# spec/features/user_uploads_dataset_spec.rb

feature "User imports datasets" do
  scenario "successfully" do
    visit root_path
    click_on("Import new datasets")

    fill_in("Name", with: "DraftKingsWeek2")
    attach_file("Salaries", Rails.root + "spec/fixtures/example_salaries.csv")
    attach_file("Rankings", Rails.root + "spec/fixtures/example_rankings.csv")
    click_on("Import")

    expect(page).to have_css('p', text: 'Datasets imported successfully!')
    expect(Dataset.where(salaries_filename: "example_salaries.csv")).to exist
  end

# ...
end
{% endhighlight %}

We can see quite clearly that even with just two scenarios, it's initially hard to figure out exactly whats going on in these tests. The reader is forced to abstract each step on the fly.

The first step to breaking down this multi-level test is to separate it out into phases. Thoughbot approaches this by defining all their tests into [four phases](https://robots.thoughtbot.com/four-phase-test). We'll follow that convention for this exercise by separating out each step within a scenario into either the setup, exercise, verify or teardown phase. In this case we don't need to teardown any part of our test so we'll stick with just the first three phases. Here's what I get:

{% highlight ruby %}
# spec/features/user_uploads_dataset_spec.rb

feature "User imports datasets" do
  scenario "successfully" do
    # setup
    visit root_path
    click_on("Import new datasets")

    # exercise
    fill_in("Name", with: "DraftKingsWeek2")
    attach_file("Salaries", Rails.root + "spec/fixtures/example_salaries.csv")
    attach_file("Rankings", Rails.root + "spec/fixtures/example_rankings.csv")
    click_on("Import")

    # verify
    expect(page).to have_css('p', text: 'Datasets imported successfully!')
    expect(Dataset.where(salaries_filename: "example_salaries.csv")).to exist
  end

# ...
end
{% endhighlight %}

Now that we have a good separation of behavior in our scenarios, we can start to abstract the logic into concise methods. 

**Single layer of abstraction**

{% highlight ruby %}
# spec/features/user_uploads_dataset_spec.rb

feature "User imports datasets" do
  scenario "successfully" do
    visit_new_dataset_page
 
    create_dataset(name: "DraftKingsWeek2", salaries_file: "example_salaries.csv", rankings_file: "example_rankings.csv")

    expect(page).to have_message('Datasets imported successfully!')
    expect(Dataset.where(salaries_filename: "example_salaries.csv")).to exist
  end

# ...

  def visit_new_dataset_page
    visit root_path
    click_on("Import new datasets")
  end

  def create_dataset(args)
    name          = args[:name]
    rankings_file = args[:rankings_file]
    salaries_file = args[:salaries_file]

    fill_in("Name", with: name) if name
    attach_file("Salaries", Rails.root + "spec/fixtures/#{salaries_file}") if salaries_file
    attach_file("Rankings", Rails.root + "spec/fixtures/#{rankings_file}") if rankings_file
    click_on("Import")
  end

  def have_message(message)
    have_css('p', text: message)
  end
end
{% endhighlight %}

This refactored spec abstracts each piece of functionality into specific methods that improve readibility and reduces context-switching for readers. The reader can now read each scenario and understand each step logically. The cost of implementing this refactor is not cheap, but I argue that the test suite is better organized, easier to read, and easier to change later on.

What are your thoughts on single level test abstractions and what other methods have you implemented in your test suites?
