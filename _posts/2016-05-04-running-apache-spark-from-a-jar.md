---
author: simon.jonassen@gmail.com
comments: true
date: 2016-05-04 23:05:00+01:00
layout: post
title: Running Apache Spark from a JAR
tags: [java, programming, spark, maven]
share: true
---
[Apache Spark](http://spark.apache.org/) is an open source cluster computing framework, which is becoming extremely popular these days. By now it has taken over the role of many previously used MapReduce and Machine Learning frameworks. So far there exists [plenty of recepies](http://spark.apache.org/docs/latest/) on how to launch a cluster and get the examples and shell running from there. Nevertheless, assume that for an educational purpose or any other odd reason we would like to build a single JAR, with all dependencies included, which then runs some Spark related code on its own. In that case, here is a simple four-step recipe to get started from scratch.<!--more-->

### Create a new Maven Java project

The easiest way to do this is from the command line (look [here](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) for an explanation):

{% highlight sh %}
mvn archetype:generate -DgroupId=com.simonj.demo -DartifactId=spark-fun -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
{% endhighlight %}

### Edit the POM file

In [my example](https://github.com/s-j/spark-fun/blob/master/pom.xml), I first explicitly state the Java version, 1.8. Then, I remove the junit dependency and add dependencies to spark-core_2.10, testng and guava (note version 16.0 to avoid the conflicts with the current version of spark-core). Finally, I use the Maven shade plugin to include the dependencies, with additional filters and transformers to get  this stuff working.

### Import the project into an IDE and edit the files

In the next step, I import the project into [Eclipse](https://eclipse.org/) and edit [App.java](https://github.com/s-j/spark-fun/blob/master/src/main/java/com/simonj/demo/App.java) and [AppTest.java](https://github.com/s-j/spark-fun/blob/master/src/test/java/com/simonj/demo/AppTest.java). The important part here is using something like the following (here I launch a new Spark context with a local master):

{% highlight java %}
try (JavaSparkContext context = new JavaSparkContext("local[2]", "Spark fun!")) {
    ...
    context.stop();
}
{% endhighlight %}

### Build the project and run

In the final step, I first build the project:

{% highlight sh %}
mvn clean package
{% endhighlight %}
Then create a [test file](https://github.com/s-j/spark-fun/blob/master/test.txt), and run the App.java from the command line (note that here I use the allinone.jar, which is the one with all dependencies included):

{% highlight sh %}
java -cp target/spark-fun-1.0-SNAPSHOT-allinone.jar com.simonj.demo.App test.txt
{% endhighlight %}

Finally, after a short time the example program spits out something like this:

{% highlight sh %}{took=1, lorem=4, but=1, text=2, is=1, standard=1, been=1, sheets=1, including=1, electronic=1, of=4, not=1, software=1, type=2, survived=1, book=1, only=1, s=1, desktop=1, to=1, passages=1, containing=1, and=3, versions=1, more=1, typesetting=2, essentially=1, recently=1, ipsum=4, a=2, galley=1, aldus=1, 1960s=1, simply=1, when=1, ever=1, dummy=2, with=2, 1500s=1, in=1, publishing=1, like=1, printing=1, five=1, industry=2, letraset=1, pagemaker=1, since=1, was=1, an=1, into=1, the=6, make=1, has=2, it=3, remaining=1, unknown=1, popularised=1, leap=1, unchanged=1, centuries=1, specimen=1, also=1, printer=1, release=1, scrambled=1}{% endhighlight %}
So it works – what a lovely evening and good night folks!

PS. [here is the complete project created through these steps](https://github.com/s-j/spark-fun).