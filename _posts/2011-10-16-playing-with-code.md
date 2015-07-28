---
author: simon.jonassen@gmail.com
comments: true
date: 2011-10-16 16:55:00+01:00
layout: post
title: Playing with code
description: "Examples and code for displaying images in posts."
tags: [java, programming, algorithms, puzzles]
share: true
---

I haven’t been programming algorithms for fun for the last 4, maybe 5 years. At the moment I have realized that it could be a good thing to do… So this is the first try :)

[Give you a number N, print all valid combinations of ( and ). e.g.N == 1, then print () N == 2, then print ()(), (()) N == 3, then print ()()(), (())(), ()(()), ((())) N ==](http://www.careercup.com/question?id=11556776).

###The idea:
Sequences can be viewed as numbers: 1 : 1 , 2 : 11 2, 3: 111 12 21 3, etc. This has a nice recursive property, for an x we take all the numbers y in [1,x] and append in in front of the solution for (x-y). Except x=1 and 0, which give “1” and “ ” respectively.

A simple solution is as follows:
{% highlight java %}
public class Balls {
	public static String balls(int x) {
		String ret = "";
		for (int i=1; i<=x; i++) ret += "(";
		for (int i=1; i<=x; i++) ret += ")";
		return ret;
	}

	public static String print(String prefix, int x) {
		if (x == 0) {
			return prefix + " ";
		} else if (x == 1) {
			return prefix + "() ";
		} else {
			String ret = "";
			for (int i = 1; i <= x; i++){
				ret += prefix + print(balls(i), x - i);
			}
			return ret;
		}
	}

	public static String print(int x) {
		return print("", x);
	}

	public static void main(String[] args) {
		System.out.println(1 + ":" + print(1));
		System.out.println(2 + ":" + print(2));
		System.out.println(3 + ":" + print(3));
		System.out.println(4 + ":" + print(4));
	}
}
{% endhighlight %}

Now, the problem with this solution is recursion and repeated calls to same print(String,int) and balls(int). The running time is O(2^n). A better solution is as follows (perhaps ArrayList can be swaped out with an array):

{% highlight java %}
public static String printDP(int x) {
	ArrayList prints[] = new ArrayList[x + 1];
	if (x == 0) {
		return "";
	} else if (x == 1) {
		return "()";
	}
	prints[0] = new ArrayList();
	prints[0].add("");
	prints[1] = new ArrayList();
	prints[1].add("()");
	String pres[] = new String[x + 1];
	pres[0] = "";
	for (int i = 1;i <= x; i++) {
		pres[i] = "(" + pres[i - 1] + ")";
	}
	for (int i = 2; i <= x; i++) {
		prints[i] = new ArrayList();
		for (int j = 1; j <= i; j++) {
			for (String s : prints[i - j]) {
				prints[i].add(pres[j] + s);
			}
		}
	}
	String ret = "";
	for (String s : prints[x]) {
		ret += s + " ";
	}
	return ret;
}
{% endhighlight %}

The running time is O(n^3).
