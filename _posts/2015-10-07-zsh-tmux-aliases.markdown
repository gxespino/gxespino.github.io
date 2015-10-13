---
layout: post
title:  "How Not Lose Your Zsh Aliases in Tmux"
date:   2015-10-07 01:08:17
categories: Developer Environment ZSH Vim
---

I have 'v' aliased to use 'mvim -v' and this workflow has worked well for me for a few months now. Unfortunately, while using tmux, I realized that I was losing all of my .zshrc aliases. 

I found this snippet after a few hours of searching: 

{% highlight ruby %}
SOURCE="${(%):-%N}"
while [ -h "$SOURCE" ]; do
  DIR="$( cd -P "$( dirname "$SOURCE" )" && pwd )"
  SOURCE="$(readlink "$SOURCE")"
  [[ $SOURCE != /* ]] && SOURCE="$DIR/$SOURCE"
done
{% endhighlight %}

Just add it to you .zshrc file and your zsh aliases should now work in tmux. 

I'm not sure how this works or even if this is what finally fixed my issue... but it's working for me. :) YMMV. 

Now, to figure out how to copy+past in tmux. Sigh.

