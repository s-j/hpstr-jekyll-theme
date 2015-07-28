---
author: simon.jonassen@gmail.com
comments: true
date: 2011-11-20 12:00:00+01:00
layout: post
title: K-th smallest element
tags: [algorithms, puzzles]
share: true
---

Task: Given an unsorted array of integers, find the k-th smallest entry.

Solution: this can be viewed as a reduce-and-conquer problem. The key-idea is to select an element as pivot and split the array into two sub-arrays, smaller (A) and greater (B) than pivot. Then, as long the length of A is greater or equal to k, we delegate the task to this array. Otherwise, we check if the rank of the smallest entry in B is greater or equal to k in the original array (n-\|B\|). If so, delegate the problem to B with k’ = k-(n-\|B\|). Otherwise, return the pivot value.

The worst case time of this method is O(n^2), for more information see [the Wikipedia article](https://en.wikipedia.org/wiki/Selection_algorithm).
