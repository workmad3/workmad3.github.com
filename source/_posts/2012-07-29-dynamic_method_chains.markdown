---
layout: post
title: Dynamic method chains
comments: true
categories: 
- rails 
- ruby 
- irc-question
---
Question from 29th June 2012 on #rubyonrails. Why doesn't the conditionally_include method execute any queries, while executing the send directly does?
<!-- more -->

Code provided:
{% gist 3194911 %}

Setting apart the issue of whether this is a good idea or not, the core of the problem is as follows:

- 'includes(:account)' gives you back an instance of ActiveRelation, a query-builder style object. It doesn't execute queries immediately, instead it gives you lazy query evaluation
- The each iteration in the method doesn't do anything with the method result, so the query will never be executed. On top of this, the result will just be discarded, as .each returns the collection it is called on.
- Executing a line in the console automatically calls 'inspect' on the result. This happens to be one of the methods that will execute the lazy query object, so you see the result of the query (and the query appears in your logs)

Keeping this in mind, the result to get the desired behaviour of a built query back can look a bit like:
{% gist 3195011 %}
