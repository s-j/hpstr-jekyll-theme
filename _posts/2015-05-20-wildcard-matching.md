---
author: simon.jonassen@gmail.com
comments: true
date: 2015-05-20 16:55:00+01:00
layout: post
title: Simple wildcard matching
tags: [java, algorithms, programming]
share: true
---

Some time ago, I had to write a very simple implementation for wildcard matching without text preprocessing, but assuming
that a pattern will be matched against a large number of strings. Given any particular string with no characters, we would like to know if a pattern where "\*" denotes a wildcard matches the string or not. Here is the solution I ended up with.

The simplest recursive implementation we can think of is as follows:
{% highlight java %}
public static boolean greedyMatch(String string, String pattern) {
    return greedy(string.toCharArray(), 0, pattern.toCharArray(), 0);
}

private static boolean greedy(char[] string, int stringPos, char[] pattern, int patternPos) {
  assert stringPos <= string.length && patternPos <= pattern.length;
  if (patternPos == pattern.length) {
    return stringPos == string.length;
  } else if (stringPos == string.length) {
    return pattern[patternPos] == '*'
      && greedy(string, stringPos, pattern, patternPos + 1);
  } else if (pattern[patternPos] == '*') {
    return greedy(string, stringPos, pattern, patternPos + 1)
      || greedy(string, stringPos + 1, pattern, patternPos);
  } else {
    return string[stringPos] == pattern[patternPos]
      && greedy(string, stringPos + 1, pattern, patternPos + 1);
  }
}
{% endhighlight %}

Here we split the pattern by wildcards and try to match all possible combinations. The implementation is a bit exhaustive, so we can do better than this.

{% highlight java %}
public static boolean match(String string, String pattern) {
  if (pattern.length() == 0) {
    // Empty pattern can only match an empty string.
      return string.length() == 0;
  } else if (pattern.charAt(0) == '*') {
    // Pattern starts with a wildcard.
    if (pattern.length() == 1) {
      // '*' matches any string.
      return true;
    }
    int stop = pattern.indexOf('*', 1);
    if (stop == -1) {
      // There is no other wildcard, so match the suffix.
      return string.endsWith(pattern.substring(1));
    } else {
      // There is another wildcard - get the token between these two.
      String token = pattern.substring(1, stop);
      int match = string.indexOf(token);
      if (match == -1) {
        // The token does not match in string.
        return false;
      } else {
        // The token matches at least once. Thus check if the rest of the pattern matches the
        // rest of the string, or(!) if the pattern matches anywhere later in the string.
        return match(string.substring(match + token.length()), pattern.substring(stop))
          || (string.length() > match && match(string.substring(match + 1), pattern));
      }
    }
  } else {
    // Pattern does not start with a wildcard, match the prefix.
    int stop = pattern.indexOf('*');
    if (stop == -1) {
      // No wildcard - do a full match.
      return string.equals(pattern);
    } else {
      // Wildcard - match the prefix and reapply the rest
      if (string.length() >= stop && pattern.substring(0, stop).equals(string.substring(0, stop))) {
        return match(string.substring(stop), pattern.substring(stop));
      } else {
        return false;
      }
    }
  }
}
{% endhighlight %}

Now we can check correctness and compare the two alternatives to a regex
implementation using 5000 randomly generated strings and 200 randomly generated patterns:
{% highlight java %}
public static void main(String[] args) {
  List parts = ImmutableList.of("b", "a", "n", "ba", "na", "*");
  Random rand = new Random(0);

  List strings = Lists.newArrayList();
  for (int i = 0; i < 5000; i++) {
    StringBuilder builder = new StringBuilder();
    int numParts = rand.nextInt(50);
    for (int j = 0; j < numParts; j++) {
      builder.append(parts.get(rand.nextInt(parts.size() - 1)));
    }
    strings.add(builder.toString());
  }

  List patterns = Lists.newArrayList();
  for (int i = 0; i < 200; i++) {
    StringBuilder builder = new StringBuilder();
    int numParts = rand.nextInt(10);
    for (int j = 0; j < numParts; j++) {
      builder.append(parts.get(rand.nextInt(parts.size())));
    }
    patterns.add(builder.toString());
  }

  List regexPatterns = Lists.newArrayList();
  for (String pattern : patterns) {
    regexPatterns.add(Pattern.compile(pattern.replace("*", ".*"), Pattern.CASE_INSENSITIVE));
  }

  for (String string : strings) {
    for (int i = 0; i < patterns.size(); i++) {
      boolean result = match(string, patterns.get(i));
      if (result != regexPatterns.get(i).matcher(string).matches()) {
        System.err.println("Match missed case :" + patterns.get(i) + " -> " + string + " (" + result + ")");
      }
    }
  }

  for (String string : strings) {
    for (int i = 0; i < patterns.size(); i++) {
      boolean result = greedyMatch(string, patterns.get(i));
      if (result != regexPatterns.get(i).matcher(string).matches()) {
        System.err.println("Simple missed case :" + patterns.get(i) + " -> " + string + " (" + result + ")");
      }
    }
  }
  System.out.println("Done correctness check!");

  long start = System.currentTimeMillis();
  for (String pattern : patterns) {
    for (String string : strings) {
      match(string, pattern);
    }
  }
  long stop = System.currentTimeMillis();
  System.out.println("Match: " + (stop - start) + " millis");

  start = System.currentTimeMillis();
  for (String pattern : patterns) {
    for (String string : strings) {
        greedyMatch(string, pattern);
    }
  }
  stop = System.currentTimeMillis();
  System.out.println("Greedy: " + (stop - start) + " millis");

  start = System.currentTimeMillis();
  for (Pattern pattern : regexPatterns) {
    for (String string : strings) {
      pattern.matcher(string).matches();
    }
  }
  stop = System.currentTimeMillis();
  System.out.println("Regex: " + (stop - start) + " millis");
}
{% endhighlight %}

The resulting elapsed times are: Simple Match - 1509, Greedy - 1806, Regex - 4969 millis.
