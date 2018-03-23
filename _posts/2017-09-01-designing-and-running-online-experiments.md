---
author: simon.jonassen@gmail.com
comments: true
date: 2017-09-01 00:00:00+01:00
layout: post
title: Starting with online experiments
tags: [statistics, experimentation, data science, todayilearned, bunchoflinks]
share: true
---

Facebook has previously released [PlanOut](https://facebook.github.io/planout), a platform for Online field experiments - see [the original paper describing PlanOut](http://hci.stanford.edu/publications/2014/planout/planout-www2014.pdf) and [its presentation](https://www.youtube.com/watch?v=Ayd4sqPH2DE), as well as [a nice presentation ](https://www.slideshare.net/seanjtaylor/implementing-and-analyzing-online-experiments) and [a great tutorial](http://eytan.github.io/www-15-tutorial/) about implementing and analysing online experiments at Facebook. This information can be used to underarstand how to deploy online experiments into a product, for example how to store and represent an experiment with a variety of options and how to assign those to users in a controlled and consistent fashion.

Furthermore there is a series of interesting publications from Microsoft ([a survey](https://ai.stanford.edu/~ronnyk/2009controlledExperimentsOnTheWebSurvey.pdf), [a paper](http://exp-platform.com/Documents/GuideControlledExperiments.pdf) and [another paper](http://exp-platform.com/Documents/2009-ExPpitfalls.pdf)) explaining numerous caveats of running controlled experiments on the web. In particular it explains statistical significance, power, and several types of mistakes it is possible to run into.

There are several other interesting guides explaining A/B testing and its pitfalls in an accessible manner:
* [A/B tests. How to make it reasonable (Edrone)](http://blog.edrone.me/en/ab-test-email-marketing-automation-crm/)
* [The ultimate guid to A/B testing (Unbounce)](http://www.datascienceassn.org/sites/default/files/A-B%20Testing%20Guide.pdf)
* [Why Every Internet Marketer Should Be a Statistician (Analytics Toolkit)](http://blog.analytics-toolkit.com/2014/why-every-internet-marketer-should-be-a-statistician/)
* [Type 1 and 2 errors, and how large should your A/B test sample size be (Visual Website Optimizer)](https://vwo.com/blog/how-to-calculate-ab-test-sample-size/) 

Fianlly, several online calculators for various computations that do not require a PhD in statistics can be found here: 
* [A/B test analysis](http://thumbtack.github.io/abba/demo/abba.html)
* [sample size](http://www.evanmiller.org/ab-testing/sample-size.html) 
* [experiment duration](https://vwo.com/ab-split-test-duration/) 
* [comparing two rates (chi-squared test)](http://www.evanmiller.org/ab-testing/chi-squared.html) 
* [comparing two series (t-test)](http://www.evanmiller.org/ab-testing/t-test.html)
