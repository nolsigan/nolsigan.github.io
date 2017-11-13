---
layout: post
date: 2017-11-12
title: [Paper Short Summary] Dynamic Routing Between Capsules
---

Introduces new concept called capsule whose output represents the instantiation parameters of a specific type of entity such as an object or object part. Output’s orientation represents its instantiation parameters and length represents its probability.

## Key Points

* Lower level capsule influences higher level capsule if two capsule’s outputs agrees
 * Agreement is caculated by scalar product of outputs
* Uses shrinking algorithm to make output vector size from 0 to 1
* Total loss is sum of each class’s loss
* Because there’s no max pooling, capsule network can capture not only the most significant object but also others
 * Even two digits highly overlapped
 * But also generates drawback that images with high variance background are hard to classify
* Network depends more on forward propagation through routing-by-agreement
 * Reduce burden for backpropagation to do the whole learning job

## Thoughts

* Nice, interesting, novel method
* Hard to predict if this will substitute CNN completely
* But giving more responsibility for making prediction to dynamic forward propagation seems like a good direction of progress
* But takes longer time to train
