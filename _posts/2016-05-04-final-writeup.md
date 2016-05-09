---
title:  "ParaRec Final Report"
date:   2016-05-04 00:00:00
categories: [Final]
tags: [Final]
---

# Summary

We have implemented and optimized a parallel collaborative filtering engine which features:

- A __compact data structure and related algorithms__ that we invented (we have __not__ read about any data compression work in collaborative filtering papers)
- A __multi-GPU__, matrix-based solution
- __Parallel locality sensitive hashing__ preprocessing algorithm for user clustering

Our deliverables will include:

- Performance graph of multiple algorithms we have implemented.

Here is a link to our final presentation slides:

- [Slides](https://drive.google.com/file/d/0B4lG-7EeCB0cVV9WQnFNOEtXUDg/view?usp=sharing)

# Background and Approach and Results

We have put up separate writeups for each optimization technique we used. __Detailed explanations__ of the optimization techniques, algorithms, designs, and results can be found in the __following posts__, __please read__:

- [CUDA Matrix](http://pavelkang.github.io/418finalproject/2016/cuda/)
- [Compressed Data Structure](http://pavelkang.github.io/418finalproject/2016/datacompression/)
- [Locality Sensitive Hashing](http://pavelkang.github.io/418finalproject/2016/nearestneighbor/)

# More Results
- We measured the the performance using the ```CycleTimer.h``` in all previous assignments.
- For the matrix solution, the precise setup is in the slides above. For the compressed data structure and nearest neighbor search implementations, they are tested on latedays cluster.
- Graphs can be found in those reports.
- We have datasets from Movielens ranging from 100k to 10m.
- As to what limits our speedup, please read those reports above.

# References

- [Streaming Similarity Search over one Billion Tweets using Parallel Locality-Sensitive Hashing](http://istc-bigdata.org/plsh/docs/plsh_paper.pdf)
- [Large-scale Parallel Collaborative Filtering for the Netflix Prize](http://www.grappa.univ-lille3.fr/~mary/cours/stats/centrale/reco/paper/MatrixFactorizationALS.pdf)
- [Standford Data Mining Textbook](http://infolab.stanford.edu/~ullman/mmds/ch3.pdf)

# List of work

We have done equal amount of work for this project
