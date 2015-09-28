---
layout: post
title: "Spiking Tips"
date: 2015-09-28 15:55:40
categories: Ruby TDD Tips
---

This one took me a while to figure out but if you're [spiking](http://www.extremeprogramming.org/rules/spike.html) and you want access to you're Rails models and database inside of your script, you can simply create a spike in your root folder and run it with **rails runner** as a prefix. 

Couldn't be any easier. 
