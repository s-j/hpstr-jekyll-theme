---
author: simon.jonassen@gmail.com
comments: true
date: 2014-08-17 16:55:00+01:00
layout: post
title: Float represenation in Java
tags: [java, programming, numbers]
share: true
---
This example illustrates how to extract the sign (the leftmost bit), exponent (the 8 following bits) and mantissa (the 23 rightmost bits) from a float in Java.

{% highlight java %}
int bits = Float.floatToIntBits(-0.005f);
int sign = bits >>> 31;
int exp = (bits >>> 23 & ((1 << 8) - 1)) - ((1 << 7) - 1);
int mantissa = bits & ((1 << 23) - 1);
System.out.println(sign + " " + exp + " " + mantissa + " " +
  Float.intBitsToFloat((sign << 31) | (exp + ((1 << 7) - 1)) << 23 | mantissa));
{% endhighlight %}

The same approach can be used for double’s (11 bit exponent and 52 bit mantissa).
{% highlight java %}
long bits = Double.doubleToLongBits(-0.005);
long sign = bits >>> 63;
long exp = (bits >>> 52 & ((1 << 11) - 1)) - ((1 << 10) - 1);
long mantissa = bits & ((1L << 52) - 1);
System.out.println(sign + " " + exp + " " + mantissa + " " +
  Double.longBitsToDouble((sign << 63) | (exp + ((1 << 10) - 1)) << 52 | mantissa));
{% endhighlight %}

For more information look [here](http://www.artima.com/underthehood/floatingP.html).
