---
title:  "Checkpoint 1"
date:   2016-03-27 15:04:23
categories: [checkpoint]
tags: [checkpoint]
---

###### Instead of focusing on collaborative filtering then matrix factorization, we decided to implement both serial versions so that we can parallelize our work after the checkpoint. **Here is a list of things we have already finished**:

## A Serial Implementation of Collaborative Filtering and Performance Analysis
- Code is available here at [Github](https://github.com/pavelkang/cf/blob/master/cf/main.cpp)
- Algorithm explanation:
  1. We calculated pairwise user similarity using the [Pearson Correlation](http://grouplens.org/blog/similarity-functions-for-user-user-collaborative-filtering/)
  2. To recommend to user $$u$$, for any other user $$u'$$ different from $$u$$, we look at the items $$u'$$ has rated, weight them by $$u'$$'s rating and the similarity between $$u$$ and $$u'$$, and use this as the predicted score for user $$u$$.
  3. We ran the algorithm on the $$ml-100k$$ dataset mentioned in the proposal
  - We ran ```gprof``` on this serial implementation of the code, the result is available here: [gist](https://gist.github.com/pavelkang/4c3a9ae32699fe0d4b1a3544c685ceb2)

## A CUDA Implementation of Collaborative Filtering and Performance Analysis
- Code is available here at [Github](https://github.com/pavelkang/cf/tree/cuda-cf/cuda-cf)

## A Serial Implementation of SVD Matrix Factorization and Performance Analysis
- Code is available here at [Github](https://github.com/pavelkang/cf/blob/master/mf/main.cpp)

## API
Our API is available at [Apiary](https://docs.pararec2.apiary.io). We designed this API so that even if we don't have time to furnish the frontend, we still have a RESTful service available.

## Server and Front-end
- Code is available here at [Github](https://github.com/jeff95723/418FinalServer)

## Issues and Concerns
- We decided not to work on the matrix factorization because we see a lot of possible optimization techniques on this one.

## Goals and Deliverables
- We won't be implementing the parallel matrix factorization algorithm. Instead, we will focus more on the collaborative filtering algorithm.

## Future Plans
- We are going to try these techniques on the collborative filtering algorithm next:
  1. Data Compression
  2. Multi-GPU (we have two)
  3. CuBLAS
  3. Faster matrix multiplication so that the amortized cost of recommending multiple users is low
  4. Sorting optimization (Since we only need the $$topn$$ recommendations, we don't have to sort everything)
  5. Use K-nearest Neighbor algorithm to find clusters of similar users. In this way, we can approximate the final result.
