---
layout: post
date: 2016-07-23
title: daily blog 07/23
tags: [daily blog, neural network]
---

## Neural Network

### Cross-entropy cost function

기존 neural net의 학습 속도를 낮추는 원인 중 하나는 quadratic cost function의 사용이다.
Quadratic cost function은 학습 속도가 sigmoid neuron의 기울기에 비례하게 되는데, output이 0, 1에 가까울 때 기울기가 작아 학습 속도도 느려진다.

Cross-entropy cost function의 경우 weight, bias에 대한 cost의 편미분 값이 오차에 비례하게 되어 오차가 클 수록 학습 속도가 빨라지는 좋은 효과를 볼 수 있다.
이는 나중에 neuron saturation에 대해 깊게 다룰 때에도 중요한 요소로 작용한다.

### Softmax

Softmax는 학습 속도 저하를 해결하는 또 다른 방법으로 한 layer의 output 값들의 합이 1이 되도록 output의 합으로 각각 나눠준다.
output의 합이 1이므로 probability distribution으로 이해할 수 있다.

Softmax는 log-likelihood cost function과 함께 사용되며 sigmoid가 cross-entropy와 함께 사용되는 것과 비슷한 성능을 낸다.
Output activation 값을 probability로 얻고 싶은 경우에 softmax의 사용이 더 적절하다.
