---
author: simon.jonassen@gmail.com
comments: true
date: 2011-10-18 12:00:00+01:00
layout: post
title: All subsets of an array
tags: [java, programming, algorithms, bit-twiddling]
share: true
---

Finding all possible, non-empty subsets of an array is actually a really simple task. Everything you need is a counter between 1 and 2^n-1, where n is the number of entries. For each possible value of the counter check it’s bits - 1 at position i means that i'th value of the array is present in the particular subset. No recursion, only shifts and additions.

{% highlight java %}
int[] numbers = { 1, 2, 3, 4 };
int n = numbers.length;
int np = 1 << n;
for (int i = 1; i < np; i++) {
	int bitv = i;
	int pos = 0;
	while (bitv > 0) {
		if ((bitv & 1) == 1) {
			System.out.print(numbers[pos] + " ");
		}
		bitv >>= 1;
		pos++;
	}
	System.out.println();
}
{% endhighlight %}
