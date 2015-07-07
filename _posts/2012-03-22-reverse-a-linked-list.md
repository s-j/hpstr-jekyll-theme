---
author: simon.jonassen@gmail.com
comments: true
date: 2012-03-22 13:00:00+01:00
layout: post
title: Reverse a linked list
tags: [java, programming, algorithms, puzzles]
share: true
---
I've been into a couple of interviews during the last two weeks. Today I got an interesting question: “reverse a single-linked list, in place”. The solution I suggested was good, but not great - “a better solution would use non-static recursion and be only two lines long”. After some thinking on the way home, I think the answer could look like this (updated version):

{% highlight java %}
public class LinkedList {

  public String s;
  public LinkedList tail;

  public LinkedList(String s) {
    this.s = s;
    this.tail = null;
  }

  public LinkedList add(String s) {
    if (this.tail == null) {
      this.tail = new LinkedList(s);
    } else {
      this.tail.add(s);
    }
    return this;
  }

  public LinkedList reverse() {
    if (this.tail == null) {
      return new LinkedList(this.s);
    } else {
      return this.tail.reverse().add(this.s);
    }
  }

  @Override
  public String toString() {
    if (this.tail != null) {
      return s + " -> " + tail;
    } else {
      return s;
    }
  }

  public static void main(String[] args) {
    System.err.println(new LinkedList("s").add("i").add("m").add("o").add("n").reverse());
  }
}
{% endhighlight %}
