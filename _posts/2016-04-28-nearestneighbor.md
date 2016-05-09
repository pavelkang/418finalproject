---
title:  "CUDA-based Approximate Nearest Neighbor Search with Locality Sensitive Hashing"
date:   2016-04-28 15:04:23
categories: [data]
tags: [data]
---

## Motivation

If we think about the first step in collaborative filtering algorithm, it calculates the pairwise user similarity. However, we only care about the most similar users in the second step, because the rest will have very small weights and contribute very little to the final result. Intuitively, it can be explained as: we only care about the top n most similar users, who cares what does the 1000th most similar user like?

Therefore, we want to **cluster** our users so that we can do nearest neighbor queries fast. Essentially, we want to build a data structure such that it supports the function ```find_most_similar_users(target_user, n)```.

## Brute Force Solution Benchmark
The brute force solution will be calculating pairwise distances using our **pearson_correlation**, and sort by the distance from small to large. To serve a query for user ```target_user``` and ```n```, we simply take the top ```n``` elements in the sorted list for ```target_user```. This is essentially what we used to be doing in the first step. However, notice that this does not save any work. It can be a correctness benchmark for our approximation algorithms.

## Locality Sensitive Hashing
However, let's say we only want approximate nearest neighbors, then we can take advantage of the approximation algorithm ```Locality Sensitive Hashing```. Here we attempt to implement locality sensitive hashing with CUDA, what's more, the implementation is based on the compressed data format we developed previously.

Here is an illustration of the parallel locality sensitive hashing algorithm we are going to implement:
![Locality Sensitive Hashing]({{site.url}}/assets/lsh.svg "Logo Title Text 1")

## Parallel Min-hashing

This is the original min-hashing algorithm from the [Standford Data Mining Textbook](http://infolab.stanford.edu/~ullman/mmds/ch3.pdf). We have developed our own parallel version of this algorithm using compressed data structure. The original min-hashing algorithm is item-major, we have modified it to user-major and fit it with our compressed data structure.

- Step 1. Calculate the mean rating for each user and "binarize" each user's rating using his mean using ```mean_kernel``` and ```binarize_kernel```:


```c
  mean_kernel<<<UPDIV(USER_SIZE, tpb), tpb>>>(compact_data_cuda, compact_index_cuda, mean_cuda);
  binarize<<<UPDIV(USER_SIZE, tpb), tpb>>>(compact_data_cuda, compact_index_cuda, mean_cuda);
```
- Step 2. Calculate a hashed function matrix:

```c
mean_kernel<<<UPDIV(USER_SIZE, tpb), tpb>>>(compact_data_cuda, compact_index_cuda, mean_cuda);
binarize<<<UPDIV(USER_SIZE, tpb), tpb>>>(compact_data_cuda, compact_index_cuda, mean_cuda);
```

- Step 3. We run ```lsh_kernel``` for each user

```c
for (int i = u_start; i < u_end; i+=2) {                                                                              int item = compact_data[i];                                                                                         int rating = compact_data[i+1];                                                                                     if (rating == 0)                                                                                                      continue;                                                                                                         for (int j = 0; j < 100; j++) {                                                                                       // for all possible hash functions                                                                                  int hashed_item = hash[item * 100 + j];                                                                             sigs[tid * 100 + j] = min(sigs[tid * 100 + j], hashed_item);                                                      }                                                                                                                 }
```

## Results
Preprocessing takes 1.1x ~ 2.5x of the compressed data structure implementation on different datasets. After preprocessing, recommend() query takes almost no time.
