---
author: simon.jonassen@gmail.com
comments: true
date: 2016-05-19 19:32:00+01:00
layout: post
title: Getting started with JMH
tags: [java, programming, profiling, performance, jmh]
share: true
---
[JMH](http://openjdk.java.net/projects/code-tools/jmh/) caught my attention several times before, but I never had time to try it out. Although I have written several micro-benchmarks in the past, for example for [my master thesis](http://daim.idi.ntnu.no/show.php?type=masteroppgave&id=4197), I doubt that any of them were as technically correct as what JMH delivers. For a brief introduction I would highly recommend to look at [this presentation](https://vimeo.com/78900556), these blogposts –  [one](http://tutorials.jenkov.com/java-performance/jmh.html), [two](http://nitschinger.at/Using-JMH-for-Java-Microbenchmarking/),
[three](http://javapapers.com/java/java-micro-benchmark-with-jmh/), and [the official code examples](http://hg.openjdk.java.net/code-tools/jmh/file/tip/jmh-samples/src/main/java/org/openjdk/jmh/samples/). More advanced examples can be found in [Aleksey Shipilёv](http://shipilev.net/)'s and [Nitsan Wakart](http://psy-lob-saw.blogspot.no/p/jmh-related-posts.html)'s blog posts. In the following, I write a simple benchmark to test a hypothesis that bothered me for a while.<!--more-->

First, I generate the benchmark project with Maven and import it into Eclipse.

{% highlight sh %}
mvn archetype:generate -DinteractiveMode=false -DarchetypeGroupId=org.openjdk.jmh -DarchetypeArtifactId=jmh-java-benchmark-archetype -DgroupId=com.simonj -DartifactId=first-benchmark -Dversion=1.0
{% endhighlight %}

Then, I would like to test the following. Given an array of sequentially increasing integers and that we would like to count the number of distinct numbers, the simples solution is to use a for loop and a condition on the equality of the consecutive elements, in other words:

{% highlight java %}
int uniques = numbers.length > 0 ? 1 : 0;
for (int i = 0; i < numbers.length - 1; i++) {
    if (numbers[i] != numbers[i + 1]) {
        uniques += 1;
    }
}
{% endhighlight %}

However, we can utilize the fact that the difference between the two consecutive and non-equal elements will be negative, and thus we can just shift the sign bit in the rightmost position and increment the counter by it, or in other words:

{% highlight java %}
int uniques = numbers.length > 0 ? 1 : 0;
for (int i = 0; i < numbers.length - 1; i++) {
    uniques += (numbers[i] - numbers[i + 1]) >>> 31;
}
{% endhighlight %}

The latter eliminates the inner branch and therefore a potential branch penalty. Hence, it *should* be faster, but is it really so? That is exactly what we can test with the following benchmark:

{% gist a35f2b157bc0c934fa5aa4c6c7ae896b %}

Here, I try both variants on an array with 1 000 000 random numbers in range of 0 to bound. I try the following bounds, 1 000, 10 000, 100 000, 1 000 000, 10 000 000, 100 000 000, to simulate the actual cardinality of the generated data set. On my macbook, it gives the following results:

{% highlight sh %}
Benchmark                   (bound)   (size)  Mode  Cnt     Score     Error  Units
MyFirstBenchmark.testBranched       1000  1000000  avgt   20   764.576 ± 150.049  us/op
MyFirstBenchmark.testBranched      10000  1000000  avgt   20   837.783 ± 143.009  us/op
MyFirstBenchmark.testBranched     100000  1000000  avgt   20  1598.128 ±  17.773  us/op
MyFirstBenchmark.testBranched    1000000  1000000  avgt   20  1209.819 ±  39.535  us/op
MyFirstBenchmark.testBranched   10000000  1000000  avgt   20  1068.606 ±  42.052  us/op
MyFirstBenchmark.testBranched  100000000  1000000  avgt   20   625.952 ±  24.321  us/op
MyFirstBenchmark.testShifted        1000  1000000  avgt   20   973.910 ±  41.843  us/op
MyFirstBenchmark.testShifted       10000  1000000  avgt   20   966.573 ±  30.002  us/op
MyFirstBenchmark.testShifted      100000  1000000  avgt   20   966.102 ±  17.895  us/op
MyFirstBenchmark.testShifted     1000000  1000000  avgt   20   973.528 ±  24.396  us/op
MyFirstBenchmark.testShifted    10000000  1000000  avgt   20   957.287 ±  31.399  us/op
MyFirstBenchmark.testShifted   100000000  1000000  avgt   20  1049.479 ± 108.593  us/op
{% endhighlight %}

This shows that for both very large and very small cardinalities the branched code is significantly faster than the shifted one, although somewhere in the middle it is indeed significantly slower. The reason to this is of course the branch prediction (read the answers to [this question on StackOverflow](http://stackoverflow.com/questions/11227809/why-is-processing-a-sorted-array-faster-than-an-unsorted-array) for details), and it illustrates exactly why we cannot assume that branch elimination by bit-twiddling will always improve the performance.

So much for the first try! This was easy and fun, and I will definitely look more into JMH in future. And by the way, JMH can also do code profiling via the -prof &lt;profiler&gt; and -lprof options (the latter lists the available profilers).
