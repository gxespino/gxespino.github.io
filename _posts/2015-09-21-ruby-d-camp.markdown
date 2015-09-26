---
layout: post
title: "Ruby DCamp"
date: 2015-09-21 15:05:40
categories: Ruby Community
---

This weekend I went to [Ruby DCamp](http://rubydcamp.org), a 3-4 day _unconference_/code retreat for Rubyists to congregate, pair program, and discuss topics related to the Ruby/software development world.

At Ruby DCamp I met dozens of inspiring and unique individuals. From self-taught devs who fell into programming as their second or third careers, to former teachers and social activists who use code as tools to better the world around them. The main self-reflection I'm left with is the renewed motivation to further my progress as a software developer and to continually chase new technologies that intrigue me.

##Code Retreat:

The first day of Ruby DCamp is a code retreat structured into 45 minute pairing sessions in which you and your pair try to create [Conway's Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life). After each session there's a mini retrospective followed by another pairing session with a new partner. Being my first code retreat, and coming into the weekend with intense anxiety regarding pair programming, I studied up on GoL beforehand by starting my own version of the program. I now wish I didn't as this would have forced me out of my comfort zone just a little bit more.

Either way, by the end of day 1, and six pairing sessions later, I was completely exhausted both mentally and physically. The exposure to programmers of varying skill levels, styles, and tools as well as the constant discomfort brought about by pairing with someone you've just met resulted in a ton of learning on my part. Some key takeaways (I may be confusing a few details regarding partners but the takeaways remain the same):

#####Partner: Josef Diago

Simply put, Josef is a vim master. Having switched to vim recently, I left with a ton of tricks to better my vim-fu. Regarding GoL, the approach we took was one that I was most familiar with, the inside-out approach, meaning we started with the most granular of tests and worked our way outward.

We started by considering a Cell object and writing out tests for what a Cell does and what messages it should send to other objects. We worked our way up to considering a Grid object that initialized with an array of Cell objects. 

#####Partner: Josh Langholtz

After the first retrospective, Evan Light, the organizer of Ruby DCamp, talked about his progression with solving GoL. He too started out by approaching the problem from inside-out but later found some merit with an outside-in approach. 

Josh and I agreed to approach the problem form an outside in approach and this led us to creating a top-level integration test for each specific Game of Life rule as opposed to starting with granular, cell specific tests. 

This approach was _refreshing_ and it forced me to think of GoL from a different perspective. It forced me to let go of my assumptions regarding what objects are needed ahead of time and to let my code evolve freely. One interesting thing we started but didn't finish due to time was the creation of a Rule Object as well as specific rules that inherited from it in the form of subclasses. Josh and I agreed to stash our code and to finish it sometime in the future. 

#####Partner: Lasitha Ranatunga

Lasitha was, if I recall correctly, the last partner I paired with and unfortunately for him my brain was literally mush by this point. Despite that, I still took away a lot from out session. The key takeaway was to always challenge the assumptions I make regarding data structures. Up until partnering with Lasitha (my vote for having the coolest name at Ruby DCamp btw), I had always used arrays to organize the Cells and Grids in GoL. Lasitha offered up the alternative of using hashes to store the Cells and their info. Instantly, I recognized the elegance of this solution and how it simplified a lot of things regarding the app's design.

Lasitha and I also agreed to stash our code and to finish it sometime in the future.

##The Unconference

The last two days of DCamp consisted of round table discussions on topics voted on by the group. There are two sessions going on simultanteously, a "Hack" session that involved more technical topics and a "Yack" session that leaned towards more philosophical/soft topics. I offered up the suggestion of two topics (Ruby Design Principles: What, When, How, Why & Why Not, as well as Algorithms for the Non-CS Major) and to my surprise, they were both accepted! I took away a lot from several sessions: 

**No One Hires Juniors:** Betsy Haibel directed a conversation on the state of technical hiring today. As a self-taught junior looking for my first dev positon, this talk was very relevant to me and it confirmed a lot of what I had encountered from my job search the past month. I took away a few specific techniques that will help me stand out to employers but I suppose the key is to consistently work towards becoming a better programmer. Soft skills such as communication is also important. Sometimes you might not know the technical answer up front but being able to communicate this and having an idea of where to start looking for an answer is all you need. This talk was too short and there definitely needs to be a forum to continue the conversation post Ruby DCamp.

**Ruby Design Patters: How, When, Why & Why Not:** I've been reading a lot about design patterns lately and this inspired me to lead a session on how they are implemented in Ruby. For the most part I let the more experienced devs steer the conversation into how they apply design patterns into their code bases. My lightbulb moment from this session was when Chris Hoffman suggested "embracing duplication" in my code and avoiding early optimization until it was painfully obvious which optimization/pattern was most necessary. Up until now I have always forced my code into a particular design (oftentimes the one I most recently read about) and this led to a lot of assumptions made up front. Incremental design and YAGNI are the key takeaways. 

**Algorithms for the Non-CS Major:** My motivation for suggesting this topic was to help juniors such as myself better understand what algorithms are and what we need to know about them from a first job perspective. The consensus was that you don't need to know algorithms specifically but rather know when they are important to consider (speed wise) and how you would go about finding more information when needed. Essentially, algorithms are code _recipes_ that are followed to solve a specific problem.

**Cool Projects:** I attended this session with the goal of finding new projects to work on as well as learning more about how other devs manage their side projects. For me, I often find myself starting projects constantly but then losing motivation for a number of reasons. Josh Szmadja offered up a really good tip on how to organize multiple sideprojects: Write a detailed README as soon as possible and while you're still excited about a project. Write about your motivations, what you want to accomplish and everything you're thinking about regarding the project at that moment. This will help 1-2 or even 3 years later when you have the itch to revisit an old project. I like how Josh referred to one of his projects as having had "two weeks worth of work completed over the course of a year." This led to the realization that I'm not alone when it comes to not finishing projects and that sometimes its OK.

##DCamp's Takeaway

True to it's overall theme: **DCamp leaves you wanting more.** Whether it's from the sessions you attended and the new technologies you want to explore, or the amazing people you meet and the issues they inspire you to tackle--Ruby DCamp whets your intellectual appetite.

Ruby DCamp is a wonderful late year tune up for software developers to renew their fervor for learning and growing. Thanks to Evan, the organizers, and all the attendees for an amazing experience. I hope to see all of you next year while connecting with many of you all throughout the year.



