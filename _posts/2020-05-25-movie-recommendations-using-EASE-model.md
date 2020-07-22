---
layout: post
title: Movie Recommender using Embarassigly Shallow Autoencoders for Sparse Data
subtitle: Recommend movies based on user ratings
image: /img/movie-rec/little-img.jpg
bigimg: /img/movie-rec/banner.jpg
tags: [Recommendation System, EASE, Embarassingly Shallow AutoEncoder]
comments: true
---

# About Embarrassingly Shallow Autoencoders for Sparse Data

Embarassingly Shallow AutoEncoders (*in reverse order:* EASE) is a linear model geared towards sparse data, in particular implicit feedback data for recommender systems. It was defined by Harald Steck, Senior Research Scientist at Netflix, and published in the proceedings of "The Web Conference" (WWW) 2019 under the CC-BY 4.0 license.

The model's objective is to predict the best items to recommend to an user, given a binary vector as input, that indicates which items a user has interacted with. It assumes that the data are given in terms of a sparse matrix
![Sparse-matrix](/img/movie-rec/sparse-matrix.JPG) , where *U* are sets of users, and *I* - sets of items. We know that an user interacted with an item when there is a positive value in *X*. Therefore, the matrix will have values of 0 whenever an user didn't interact with the item.

The parameters of the model are given by the item-item weight-matrix
![Weight-matrix](/img/movie-rec/weight-matrix.JPG) . The model predicts a score defined by the dot product
![Score](/img/movie-rec/score.JPG)  

where:

![Suj](/img/movie-rec/suj.JPG) - the predicted score for an item *j* given a user *u*
![Xu.](/img/movie-rec/xu.JPG) - refers to row *u*
![B.j](/img/movie-rec/bj.JPG) - refers to column *j*

Find more about the structure of the model on the [original paper](https://arxiv.org/pdf/1905.03375v1.pdf).  


# Recommend movies with EASE















