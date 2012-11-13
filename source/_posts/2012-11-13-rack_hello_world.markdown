---
layout: post
title: Rack Hello World
comments: true
categories:
- rails
- gists
- rack
- ruby
---
A minimal rack application, capable of being cloned from gist and deployed
directly to heroku.
<!-- more -->

Rack is a fairly well known piece of the Ruby ecosystem nowadays, and is
responsible for definining an interface between an HTTP request and your Ruby
web app. It's a remarkably simple interface, which can be demonstrated in
a single line:

{% gist 2712594 config.ru %}

This shows the important parts of a Rackup file - you run a rack application.
A rack application is a ruby object that responds to a single method 'call'.
This method takes a single argument of the environment for the request
(including all the information like the header, the url, query params, etc). The
call method must return an array with 3 elements - the status code, a hash of
headers, and an array of body strings.

The full project (which includes a Gemfile and Procfile for heroku) is:

{% gist 2712594 %{