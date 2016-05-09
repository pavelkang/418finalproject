---
title:  "Data Compression"
date:   2016-04-27 15:04:23
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

We want to compute the user-user similarity. And the algorithm depends on "finding common items between two users". In a sparse representation, this is very easy to do because checking whether an item is consumed by a user or not is simply $O(1)$. However, in our compressed data structure, the most naive implementation will require $O(n)$ lookup for each item. Thus, it takes $O(mn)$ to compute a pairwise user similarity where $m$ is the number of items consumed by one user and $n$ is the number of items consumed by another. To combat this problem, we have developed an $O(m+n)$ algorithm as follows: Essentially, the algorithm looks at the two data "regions" for the two users. Since the incoming data is item-sorted for each user, we can find the common items in two "regions" by maintaining two pointers starting from the respective starting points, and incrementing the one with the smaller item index. Once one pointer reaches the end, we know for sure that there won't be common items.

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

To calculate item preferences, instead of finding common items, we want to find items that are not present in one user's ratings. The idea is similar to the previous one. We keep two pointers i, j. If the item pointed by i is smaller than j, then item i is not in the other user's consumption.

```c
  while (i < u_end && j < v_end) {
    item_i = compact_data[i];
    item_j = compact_data[j];
    if (item_i == item_j) {
      i += 2;
      j += 2;
    } else if (item_i < item_j) {
      // possible item_j appear in u's ratings
      i += 2;
    } else {
      // item_i > item_j, item_j won't be rated by u
      like[item_j] += sim[tid] * compact_data[j+1];
      j += 2;
    }
  }
  if (j != v_end) {
    while (j < v_end) {
      item_j = compact_data[j];
      like[item_j] += sim[tid] * compact_data[j+1];
      j += 2;
    }
  }
```

## Performance

We have tested four implementations on two datasets of size 100k and 10m. The four implementations are: a serial CPU version, a naive CUDA implementation, a $$O(mn)$$ naive algorithm on compressed data structure, and our $$O(m+n)$$ compressed data structure.
![data structure]({{site.url}}/assets/100k.png "Logo Title Text 1")
![data structure]({{site.url}}/assets/10m.png "Logo Title Text 1")
