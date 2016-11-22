---
author: simon.jonassen@gmail.com
comments: true
date: 2016-08-21 10:10:00+01:00
layout: post
title: HyperLogLog - StreamLib vs Java-HLL
tags: [java, programming, hyperloglog, performance]
share: true
---
A while ago, I was looking at cardinality estimators for use in a distributed setting – given a data set spread over a set of nodes, we want to compute the total number of unique keys without having to transfer all keys or a global bit signature. Counting sketches such as HyperLogLog (see [here]( http://highlyscalable.wordpress.com/2012/05/01/probabilistic-structures-web-analytics-data-mining/), [here](https://research.neustar.biz/2012/10/25/sketch-of-the-day-hyperloglog-cornerstone-of-a-big-data-infrastructure/) and  [here](https://research.neustar.biz/2013/01/24/hyperloglog-googles-take-on-engineering-hll/) for an introduction) have superior memory usage and cpu performance when cardinality can be estimated with a small error margin. In the following, I summarize a comparison between the two Java libraries, [StreamLib](https://github.com/addthis/stream-lib) and [Java-HLL](https://github.com/aggregateknowledge/java-hll), I did back in February 2014. <!--more-->

## Methods ##

[StreamLib](https://github.com/addthis/stream-lib) implements several methods:

* Linear counting (`lincnt`) - hashes values into positions in a bit vector
and then estimates the number of items based on the number of unset bits.

* [LogLog](http://algo.inria.fr/flajolet/Publications/DuFl03-LNCS.pdf) (`ll`) - uses hashing to add an element to one of the m different estimators, and updates the maximum observed rank `updateRegister(h >>> (Integer.SIZE - k), Integer.numberOfLeadingZeros((h << k) | (1 << (k - 1))) + 1))`, where `k = log2(m)`. The cardinality is estimated as `Math.pow(2, Ravg) * a`, where Ravg is the average maximum observed rank across the m registers and a is the a correction function for the given m (see the paper for details).

* [HyperLogLog](http://algo.inria.fr/flajolet/Publications/FlFuGaMe07.pdf) (`hll`) - improves the LogLog algorithm by several aspects, for example by using harmonic mean.

* [HyperLogLog++](http://research.google.com/pubs/archive/40671.pdf) (`hlp`) - Google's take on HLL that improves memory usage and accuracy for small cardinalities

[Java-HLL](https://github.com/aggregateknowledge/java-hll) (`hlx`) on the other hand provides a set of tweaks to HyperLogLog, mainly exploring the idea that a chunk of data, say 1280 bytes, can be used to fully represent a short sorted list, a sparse/lazy map of non-empty register, or a full register set (see the project page for details).

## Performance comparison ##

I used two relatively small real-world data sets, similar to what was intended to be used in production. For hashing I used StreamLib's `MurmurHash.hash64`, which for some reason did it better than Guava's on the test data (I haven't investigated the reason though). The latency times given below are cold-start numbers, measured with no respect to JIT and other issues. In other words, these are **not** scientific results.

### Dataset A ###
The first data set has the following characteristics:

* 3765844 tokens
* 587913 unique keys (inserting into a `Sets.newHashSet()`: 977ms)
* 587913 unique hashed keys (`Sets.newHashSet()`: 2520ms)

First lets compare the StreamLib methods tuned for 1% error with 10 mil keys. The collected data includes the name of the method, relative error, total estimator size, total elapsed time. The number behind `ll`, `hll`, `hlp` denotes the `log2(m)` parameter:

| name   |  error  | size    | time    |
|--------|---------|---------|---------|
| lincnt |  0.0017 | 137073B |  1217ms |
| ll__14 |  0.0135 | 16384B  |   963ms |
| hll_13 |  0.0181 | 5472B   |  1000ms |
| hlp13  | -0.0081 | 5473B   |   863ms |

Here HLP performs best, with only 0.81% error and using only 5KB memory.

Now, lets compare StreamLib and Java-HLL. The parameter behind `hlp` is `log2(m)`, while the parameters behind `hlx` are `log2(m)`, register width (5 seems like the only one that works), promotion threshold (-1 denotes the `auto` mode) and the initial representation type.

| name              |  error  |   size |  time |
|-------------------|---------|--------|-------|
| hlp10             |  0.0323 |   693B | 818ms |
| hlp11             |  0.0153 |  1377B | 967ms |
| hlp12             |  0.0132 |  2741B | 790ms |
| hlp13             | -0.0081 |  5473B | 731ms |
| hlp14             | -0.0081 | 10933B | 697ms |
| hlx_10_5_-1_FULL  | -0.0212 |   643B | 723ms |
| hlx_10_5_-1_SPARSE| -0.0212 |   643B | 680ms |
| hlx_11_5_-1_FULL  | -0.0202 |  1283B | 670ms |
| hlx_11_5_-1_SPARSE| -0.0202 |  1283B | 710ms |
| hlx_12_5_-1_FULL  | -0.0069 |  2563B | 673ms |
| hlx_12_5_-1_SPARSE| -0.0069 |  2563B | 699ms |
| hlx_13_5_-1_FULL  |  0.0046 |  5123B | 702ms |
| hlx_13_5_-1_SPARSE|  0.0046 |  5123B | 672ms |
| hlx_14_5_-1_FULL  |  0.0013 | 10243B | 693ms |
| hlx_14_5_-1_SPARSE|  0.0013 | 10243B | 678ms |

Here Java-HLL is both more accurate and faster.

### Dataset B ###
The second data set has the following characteristics:

* 3765844 tokens
* 2074012 unque keys (`Sets.newHashSet()`: 1195ms)
* 2074012 unique hashed keys (`Sets.newHashSet()`: 2885ms)

StreamLib methods tuned for 1% error with 10 mil keys:

| name   | error    |  size   | time  |
|--------|----------|---------|-------|
| lincnt |   0.0005 | 137073B | 663ms |
| ll__14 |  -0.0080 |  16384B | 578ms |
| hll_13 |   0.0131 |   5472B | 515ms |
| hlp13  |  -0.0118 |   5473B | 566ms |

And StreamLib vs Java-HLL:

| name               |   error | size   |time |
|--------------------|---------|--------|-----|
| hlp10              |  0.0483 | 693B   |560ms|
| hlp11              |  0.0336 | 1377B  |489ms|
| hlp12              | -0.0059 | 2741B  |560ms|
| hlp13              | -0.0118 | 5473B  |567ms|
| hlp14              | -0.0025 | 10933B |495ms|
| hlx_10_5_-1_FULL   | -0.0227 | 643B   |575ms|
| hlx_10_5_-1_SPARSE | -0.0227 | 643B   |570ms|
| hlx_11_5_-1_FULL   | -0.0194 | 1283B  |505ms|
| hlx_11_5_-1_SPARSE | -0.0194 | 1283B  |573ms|
| hlx_12_5_-1_FULL   | -0.0076 | 2563B  |500ms|
| hlx_12_5_-1_SPARSE | -0.0076 | 2563B  |570ms|
| hlx_13_5_-1_FULL   | -0.0099 | 5123B  |576ms|
| hlx_13_5_-1_SPARSE | -0.0099 | 5123B  |501ms|
| hlx_14_5_-1_FULL   | 0.0015  | 10243B |572ms|
| hlx_14_5_-1_SPARSE | 0.0015  | 10243B |500ms|

So the results are similar to those with Dataset A.

## Conclusions ##

This comparison was done more than two years ago and I was quite skeptical to both frameworks. I found many strange thins in the StreamLib (both the [reported issues](https://github.com/addthis/stream-lib/issues/64) and more), while Java-HLL did not work with other regsizes either. I settled for Java-HLL since it had a better implementation and gave better results. However, things change fast and StreamLib might have been improved a lot since then. I still want to look more at the code in both frameworks, and perhaps the frameworks that were published since then.

Nevertheless, HLL is clearly a method to use. A really nice feature of HLL is that you can have multiple counters and you can add (union) them together without loss. [Intersection, however, can be tricky]( http://blog.aggregateknowledge.com/2012/12/17/hll-intersections-2/
).

### Open question ###

The *register width* in LogLog methods is the number of bits needed to represent the position maximum position of the first 1 bit. There are `m = (beta / se)^2` such registers, where beta is a method-related constant and se is desired standard error, say 0.01. I guess this comes from `StdErr = StdDev / sqrt(N)` for a sample mean of a population (ref. [wikipedia](http://en.wikipedia.org/wiki/Relative_standard_error#Relative_standard_error)), but my knowledge of statistics is a bit too rusty to really understand this. Consequently, my understanding of the papers is that LogLog has `beta = 1.30`, HLL has `beta = 1.106` and HLL++ has `beta = 1.04`, but I might be wrong. After all StreamLib code used these three numbers completely randomly in methods and tests. When I asked what was correct, they asked me back. Honestly, I don't know :)

### The Code ###

{% gist 2303b1233b8a932f6c3803c60ce7f372 %}
