---
title:  "Data Compression"
date:   2016-01-08 15:04:23
categories: [data]
tags: [data]
---

## Motivation
One of the biggest challenge for improving the performance of a recommendation engine is how to reduce the data access time. By the nature of collaborative filtering algorithm, the data access pattern is very random. What's more, since the user rating matrix is very sparse, multiple memory loads are very likely accessing very different addresses. Therfore, a compressed data structure that exploits memory locality will be extremely helpful.

We designed and implemented a compact data structure which allows collaborative filtering algorithms to take advantage of memory locality. We have never read anything in literature about compressed data structure in collaborative filtering.

## Data Structure

![data structure]({{site.url}}/assets/illustration.svg "Logo Title Text 1")

## Algorithm Implementation

1. Calculating User Similarity with Pearson Correlation

![data structure]({{site.url}}/assets/algo.svg "Logo Title Text 1")

```c
  if (user == USER_SIZE - 1) {
    u_end = DATA_SIZE - 1;
  } else {
    u_end = compact_index[user+1];
  }
  if (tid == DATA_SIZE - 1) {
    v_end = DATA_SIZE - 1;
  } else {
    v_end = compact_index[tid+1];
  }

  int i = u_start;
  int j = v_start;
  double rui, rvi;
  int item_i, item_j;
  while (i < u_end && j < v_end) {
    item_i = compact_data[i];
    item_j = compact_data[j];
    if (item_i == item_j) { // common item
      commons++;
      rui = compact_data[i+1];
      rvi = compact_data[j+1];
      a += (rui - u_mean) * (rvi - v_mean);
      b += (rui - u_mean) * (rui - u_mean);
      c += (rvi - v_mean) * (rvi - v_mean);
      i += 2;
      j += 2;
    } else if (item_i < item_j) {
      i += 2;
    } else {
      j += 2;
    }
  }
```


2. Calculating Item Preference



## Performance

We have measured the performance only on one test set so far but it is 2x better.
