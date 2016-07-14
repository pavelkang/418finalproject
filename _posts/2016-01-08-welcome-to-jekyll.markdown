---
title:  "ParaRec Project Proposal"
date:   2016-01-08 15:04:23
categories: [Intro]
tags: [Intro]
---


## Title

__Parallel Recommendation Engine__

Team members:

- Kai Kang (kaik1)
- Jianfei Liu (jianfeil)

## Summary
We are going to implement a parallel collaborative filtering recommmendation engine on a single machine, and explore different optimization algorithms addressing memory access problems, and model parallelism using multiple GPUs.

## Background and Challenge

![recommendation engine]({{site.url}}/assets/rec.svg "Logo Title Text 1")
Recommendation system is crucial to a lot of tech companies such as Facebook, Amazon, and Netflix because it helps users quickly find what they need, and help companies best sell their products (news for Facebook, actual products for Amazon, and movies for Nextflix.) In fact, Netflix hosted a contest [_Netflix Prize_](http://www.netflixprize.com/) to award __1 millon dollars__ to the winning recommendation algorithm. To evaluate a recommendation system, we look at two factors, speed and accuracy. Speed is as important as accuracy because a huge amount of new training data come in every second and it is important to use the new data as soon as we can.

In my last internship at Bloomberg, I implemented a recommendation engine of the Bloomberg research report. However, I encountered some difficulties during the process:

- __distributed vs. single machine__: I experimented with distributed platforms such as Hadoop and Spark. One the one hand, the problem with distributed systems is the dataset itself is not large enough to cancel the overhead of using a distributed system. On the other hand, single-machine implementations suffer from memory and speed problems.

- __data parallelism__: The collaborative filtering algorithm has almost no dependencies and is easy to parallelize naively. However, the biggest challenge is that because the memory access pattern is very random, there is no locality in the naive implementation. What's more, since the dataset is very sparse, the most naive implementation might not even be runnable on a single machine (this actually happened to me). We want to address this problem by developing compressed data structure and algorithms.

- __model parallelism__: Since the problem has few dependencies, model parallelism will give us huge speedup if implemented correctly. However, distributing to multiple machines is complicated in practice especially in the development stage when all the effort should be focused on improving the algorithm. We address this problem by developing multi-GPU solutions

- __approximation algorithm__: What is more, it seems unecessary to compute all pairwise user similarities. In pratice, an approximated solution will often suffice. We will develop parallel approximation algorithms to preprocess the raw dataset.

We will be looking at the collaborative filtering algorithm in depth:

__Collaborative Filtering__

~~~
recommend(u) {
  likelihood = {}
  V = similar_user(u);
  for v in V:
    for items v purchased but u hasn't:
      likelihood[v] += similarity(u, v)
}
~~~
{: .language-c}

In _Collaborative Filtering_, getting similar users of a user can be reduced to a clustering problem where we want to cluster users by their similarities. The rest double for loop is also computation-intensive and requires effort to efficiently parallelize.

In a word, this is our roadmap:
![roadmap]({{site.url}}/assets/overview.svg "Logo Title Text 1")


__Matrix Factorization__

The idea of matrix factorization is that we have $$ M $$, where each row represents a __user__, and each column represents an __item__, and entry $$M[u][i]$$ represents the rating of user $$u$$ to item $$i$$. What we will be implementing is a matrix factorization algorithm to factor $$M$$ into user matrix $$U$$ and item matrix $$I$$, where $$U$$ and $$I$$ essentially represents the inherent attributes of each user and item. This algorithm by [Simon Funk]((http://sifter.org/~simon/journal/20061211.html) wins the Netflix-prize. Here is an example to illustrate how it works:
Imagine our $$M$$ is:

$$
 \left[
 \begin{array}{cccc}
 & \text{Titanic} & \text{Interstellar} & \text{The Notebook}\\
 Alice & 5.0 & 2.0 & 4.0\\
 Bob & 2.0 & 5.0 & 1.0\\
    \end{array}
\right]
$$

Using an online calculator, we get $$U$$ to be:

$$
 \left[
 \begin{array}{ccc}
 Alice & 1.0 & 0.0\\
 Bob & 0.4 & 1.0 &\\
    \end{array}
\right]
$$

We get $$I$$ to be:

$$
 \left[
 \begin{array}{ccc}
 \text{Titanic} & \text{Interstellar} & \text{The Notebook}\\
 5.0 & 2.0 & 4.0\\
 0.0 & 4.2 & -0.6\\
    \end{array}
\right]
$$

Matrix factorization _automatically_ extracts the features of each movie, and each person's favorite feature. In this example, it is apparent that the two features are _romance_, and _adventure_. Thus, the romantic movies have a large value for romance feature and small value for adventure feature. And Alice has a large value for romance, and Bob has a large value for adventure.

## Resources

We will be refering to the following academic papers (the list will be updated during the project):

* [Parallel Matrix Factorization for Recommender Systems](http://www.cs.utexas.edu/~inderjit/public_papers/kais-pmf.pdf)
* [Large-scale Parallel Collaborative Filtering for the Netflix Prize](http://www.grappa.univ-lille3.fr/~mary/cours/stats/centrale/reco/paper/MatrixFactorizationALS.pdf)
* [A Parallel Clustering Algorithm with MPI - MKmeans](http://www.jcomputers.us/vol8/jcp0801-02.pdf)

We will be using the following dataset for testing purposes

- [Amazon Reviews](https://snap.stanford.edu/data/web-Amazon.html)
- [Public Dataset(more than 5 categories)](https://gist.github.com/entaroadun/1653794)
- [Netflix Prize Data Set](http://academictorrents.com/details/9b13183dc4d60676b773c9e2cd6de5e5542cee9a)

## Goals and Deliverables

- A frontend web app which allows users to upload their training data and test data, and visualizes the correctness of each algorithm, (and possible each different implementation using GPU/OpenMP)
- A backend service API which implements the different algorithms (collaborative filtering, clustering)

## Platform Choice
We will be using C++ as our main programming language because we want to use CUDA and OpenMP to parallelize our program. C++ is a perfect balance between performance and code readability.
Our code will run on computers. We will try to make our code compatible with all OS.

## Schedule

- __Friday, April 8__: Have a RESTful C++ server ready, design the backend API, discuss which versions of the algorithms to implement
- __Friday, April 15__: Study, implement, and experiment clustering algorithms
- __Friday, April 22__: Use clustering algorithms to implement collaborative filtering, benchmark different implementations
- __Friday, April 29__: Implement Simon Funk's matrix factorization algorithm and connect with APIs
- __Friday, May 6__: Wrap up the project with output visualization, final writeup, and reflection
