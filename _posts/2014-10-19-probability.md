---
author: simon.jonassen@gmail.com
comments: true
date: 2014-10-19 14:55:00+01:00
layout: post
title: On Probability and Luck
tags: [statistics, numbers, luck]
share: true
---

A few weeks ago I was at the annual [JavaZone](http://2014.javazone.no/) where they had many great talks and quite many cool stands.
At one of the stands they had a spinner, a wheel with numbers 1 to 20, and if you were lucky you could win a book. The girl at the stand said
"You can choose one number and spin it twice or you can choose two numbers but only spin once". "Is there a difference?" - I asked, "No" -
the girl replied and shook her head. But really? Lets look at this...

Assume we had just one number and one spin, the probability to win is then 1/n where n is 20. Lets call it just p. Now, with two numbers the probabilty is 2/n or 2p.

With two spins it must be 1 minus the probability to loose on both attempts, that is 1-(1-p)^2. Or, put it another way, the probability to win on the
first attempt plus the probability to win on the second attempt given that the first attemp has failed, that is p+(1-p)p. Either approach gives 2p-p^2.

The difference of p^2 is actually very interesting. Why is it or what does it represent? Well, both situations can be expressed as an "A or B" outcome,
which can also be expressed as "A + B - A and B". In both situations A and B have the same probability, p, but "A and B" is however different. In the first
case we cannot win with both numbers at the same time, while in the second case it is possible, we can win on both spins.

This means that something we percieve as increasing our chance to win, in reality reduces it. It is probably why the most people at the stand chose to
spin the wheel twice. As a true scientist, I sneaked to the stand whole 7 times, chose two numbers each time and didn't win once. Well, probability and luck
have nothing to do with each other I say.
