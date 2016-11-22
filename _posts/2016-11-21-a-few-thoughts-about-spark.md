---
author: simon.jonassen@gmail.com
comments: true
date: 2016-11-21 20:10:00+01:00
layout: post
title: A few thoughts about Spark
tags: [machine learning, programming, java, python, spark,]
share: true
---
Recently I have been working on a text-classification task. Along the way I have tested out three interesting machine learning frameworks which I would like to address in the next few posts. This time I start with the [Apache Spark](https://spark.apache.org)'s MLlib.<!--more-->

Spark got my attention [quite a long time ago](http://www.slideshare.net/s-j/yet-another-intro-to-apache-spark) and it is extremely useful for data exploration tasks where you can simply put lots of data on HDFS and then use a Jupyter notebook to transform the data interactively. However, although training is preferably done offline using a large number of examples (where Spark becomes handy), the classification part is often desired to be a short-latency/high-throughput task. As the framework itself brings quite a lot of overhead, it could be nice if the API methods could be executed without the Spark cluster when necessary. In that case you could use the cluster to build a model, serialize and ship it to a worker, which will then use the model on the incoming instances. 

My earlier implementation of [text-classification for Reuters 21578](https://github.com/s-j/reuters21578) can be executed as a simple JAR, but [as I wrote earlier, it was quite a dance to do this correctly](http://s-j.github.io/running-apache-spark-from-a-jar/). Moreover, in that example I have used the RDD part of the MLlib API and ended up with a very verbose Java code.

Recently, the API has been extended with pipelines and many interesting features (most likely inspired by Scikit-Learn) making it really easy to implement a classifier in just a few lines of code, for example:

```python
labelIndexer = StringIndexer(inputCol="label_text", outputCol="label")
tokenizer = Tokenizer(inputCol="text", outputCol="tokens")
remover = StopWordsRemover(inputCol=tokenizer.getOutputCol(), outputCol="filtered")
hashingTF = HashingTF(inputCol=remover.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=20, regParam=0.01)
ovr = OneVsRest(classifier=lr)
pipeline = Pipeline(stages=[labelIndexer, tokenizer, remover, hashingTF, ovr])
model = pipeline.fit(train_paired)
result = model.transform(test_paired)
predictionAndLabels = result.select("prediction", "label")
evaluator = MulticlassClassificationEvaluator(metricName="accuracy")
print("Test set accuracy = " + str(evaluator.evaluate(predictionAndLabels)))
```
Nevertheless, this new DataFrame-based part of the API has not yet reached parity with the old RDD-based part of the API. The latter is planned to be deprecated when this happens, but currently some of the methods are available only through the old part of the API. In other words, a strong dependency between the algorithms and the underlying data structure is a real problem here. I only hope that the same will not happen again if the DataFrame concept gets replaced by a better idea in a year or two.

Finally, although there are quite many resources available online (books, courses, talks, slides, etc.), the documentation of MLlib is far from good (especially the API docs) and the customization part beyond simple examples is a nightmare (an exercise for the reader: add bigrams to the pipeline above), if possible at all. On the positive side, Spark is great for certain use cases and is being actively developed with lots of interesting features and ideas coming up next.
