---
author: simon.jonassen@gmail.com
comments: true
date: 2012-01-27 12:00:00+01:00
layout: post
title: ANSI-colored prompt in Java
tags: [java, programming]
share: true
---

A traditional way to debug programs is to carefully place print-statements here and there. However, as the amount of printed text increases the ability to filter out and understand the important information decreases. For this reasons, I have searched on how to use ANSI color codes in Java and found [a nice implementation in Jlibs](https://code.google.com/p/jlibs/wiki/AnsiColoring). Unfortunately this library did way more than what I needed, and therefore I have stripped it down into a small class that wraps a text string to add color codes and attributes. Feel free to use it and thanks a lot to the original author :-)

{% gist 3142804 %}
