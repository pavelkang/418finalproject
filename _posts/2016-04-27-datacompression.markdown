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

![data structure]({{site.url}}/assets/kai_kang.jpg "Logo Title Text 1")

## Algorithm Implementation

## Performance
