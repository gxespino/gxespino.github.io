---
layout: post
title:  "SQL: The awesomeness of SELECT DISTINCT"
date:   2015-10-13 6:08:17
categories: SQL Database
---

Let's create a movies database that stores the title, genre, year released, and imdb_rating of each movie. 

{% highlight sql %}
CREATE TABLE movies (
  id INTEGER PRIMARY KEY,
  title TEXT,
  genre TEXT,
  year_released INTEGER,
  imdb_rating INTEGER
);
{% endhighlight %}

Say we want to know every genre in our database. We can try SELECT.

{% highlight sql %}
SELECT genre FROM movies;
{% endhighlight %}

| genre         |
| ------------- |
| action        |
| action        |
| action        |
| action        |
| action        |
| drama         |
| drama         |
| drama         |
| etc...         |

OK, so looks like we're getting every genre for *every* movie in your database. And, it looks like we're going to be here a while. That's not cool considering what we really wanted was just a list of every **unique** genre in our database.

Thats where SELECT DISTINCT comes into play. Be amazed:

{% highlight sql %}
SELECT DISTINCT genre FROM movies;
{% endhighlight %}

| genre         |
| ------------- |
| action        |
| drama        |
| horror        |
| romance        |
| chick flick       |
| musical   |

Ahh, much better. So what did we learn? SELECT DISTINCT is used to return unique values in our database. It filters out all duplicate values. Here, the result set lists each genre in the movies table exactly once. Pretty useful, and awesome stuff.

*Note: SELECT UNIQUE is an old alias for SELECT DISTINCT but only for a Oracle flavored versions of SQL. Standard SQL calls for using DISTINCT so your best bet is to just forget about SELECT UNIQUE.* 



