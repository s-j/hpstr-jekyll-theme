---
author: simon.jonassen@gmail.com
comments: true
date: 2018-03-01 00:00:00+01:00
layout: post
title: On designing and running online experiments
tags: [statistics, experimentation, data science, todayilearned, bunchoflinks]
share: true
---

Have you wondered how to design and run online experiments? In particular, how to implement an experiment dashboard such as the one enpictured below (in this case Visual Website Optimizer) and how to use this in your product? Good, lets have a quick look!

![Visual Website Optimizer](http://s-j.github.io/images/vwo.png)

To begin, Facebook has previously released [PlanOut](https://facebook.github.io/planout), a platform for Online field experiments. Looking apart from the language itself, the essense of this project is in the [random.py](
https://github.com/facebook/planout/blob/master/python/planout/ops/random.py) which demonstrates how to map users or pages to the random aleternatives. In short, each experiment and variable has a salt to be added on top of the user or page id hash to prevent carry-over errors. The resulting hash is then mapped to the final value through modulo arithmetic and a set of linear transformations. Given this, it is fairly easy to design an API or a library to represent an experiment with a variety of options and to assign those to users in a controlled and consistent fashion. Or you can just use PlanOut or VWO right out of the box.

Settled with the setup and random assignment parts, the next question is how to actually design and run an experiment for your needs. For this, I highly recomment to take a quick look at [the original paper describing PlanOut](http://hci.stanford.edu/publications/2014/planout/planout-www2014.pdf) and [its presentation](https://www.youtube.com/watch?v=Ayd4sqPH2DE), as well as [a nice presentation ](https://www.slideshare.net/seanjtaylor/implementing-and-analyzing-online-experiments) and [a great tutorial](http://eytan.github.io/www-15-tutorial/) about implementing and analysing online experiments at Facebook. Furthermore, there is a series of interesting publications from Microsoft ([a survey](https://ai.stanford.edu/~ronnyk/2009controlledExperimentsOnTheWebSurvey.pdf), [a paper](http://exp-platform.com/Documents/GuideControlledExperiments.pdf) and [another paper](http://exp-platform.com/Documents/2009-ExPpitfalls.pdf)) explaining numerous caveats of running controlled experiments on the web. In particular it explains statistical significance, power, and several types of mistakes it is possible to run into.

If resarch papers sound just too dry and formal, there are several interesting guides explaining A/B testing and its pitfalls in a very accessible manner:
* [A/B tests. How to make it reasonable (Edrone)](http://blog.edrone.me/en/ab-test-email-marketing-automation-crm/)
* [The ultimate guid to A/B testing (Unbounce)](http://www.datascienceassn.org/sites/default/files/A-B%20Testing%20Guide.pdf)
* [Why Every Internet Marketer Should Be a Statistician (Analytics Toolkit)](http://blog.analytics-toolkit.com/2014/why-every-internet-marketer-should-be-a-statistician/)
* [Type 1 and 2 errors, and how large should your A/B test sample size be (Visual Website Optimizer)](https://vwo.com/blog/how-to-calculate-ab-test-sample-size/) 

Finally, when it comes to presenting and interpreting the results, [this A/B test analysis tool](http://thumbtack.github.io/abba/demo/abba.html) provides a simple view similar to the one in the screenshot above. Otherwise, there are several online calculators for various computations that do not require a PhD in statistics - [sample size](http://www.evanmiller.org/ab-testing/sample-size.html), [experiment duration](https://vwo.com/ab-split-test-duration/),[comparing two rates (chi-squared test)](http://www.evanmiller.org/ab-testing/chi-squared.html), [comparing two series (t-test)](http://www.evanmiller.org/ab-testing/t-test.html).

Have fun!
