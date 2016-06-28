---
layout: post
date: 2016-06-28
title: Basics of Neural Networks
tags: [neural network]
---

Neural network에 대해 공부한 내용을 정리해놓으려 한다.

## Neural network

ML 그리고 neural net의 최대 강점은 static한 코드와 달리 dynamic한 function을 만들 수 있다는 것이라 생각된다. Neural net의 한 neuron은 NAND gate로 표현이 가능한데 이는 곧 neural net이 하나의 가변적 논리회로임을 의미한다. 따라서 기존의 static한 함수처럼 hard coding을 하는게 아니라 학습 시키면서 하나의 함수를 동적으로 계속 발전시킬 수 있다. 가변적인 전자회로를 만드는 것은 불가능하지만 소프트웨어에서 virtual 가변회로를 만드는 느낌이다. 이 특성만으로 neural net이 얼마나 powerful한지 느낄 수 있다.

## Sigmoid Neurons

Sigmoid neuron은 간단하면서도 실용적이다. Perceptron처럼 yes or no로 선택하는게 아니라 percentage를 output으로 가지므로써 인간의 뉴런을 효과적으로 모방한다. 한 학습 사이클에서 weight과 bias의 작은 변화가 정확도에 영향을 줘야하는데 perceptron의 경우에는 그 변화가 없거나 극단적인 반면 sigmoid neuron은 효과적으로 적용된다.

## achitecture of neural net

Neural net의 구조는 ( fully connected OR partially connected ), ( recurrent OR feed forward ) 중 무엇을 선택하냐에 따라 달라진다. 가장 기본적이면서 많이 쓰이는 방법은 fully connected, feed forward 방법이다. Input도 neuron으로 취급하여 전체 구조를 Input layer, hidden layer, output layer로 구성한다. hidden layer를 몇 곱하기 몇 matrix로 할 것이냐에 따라 구조가 달라지는데 어떤 방법으로 이를 결정하는 지는 아직 모르겠다. Output layer를 어떻게 구성하냐에 따라서도 정확도가 달라지는데 handwriting recognition의 경우, 10개의 neuron이 각각 0~9의 percentage를 나타나게 하는 방식이 하나의 neuron으로 0~9 사이의 값을 나타내게 하는 것보다 더 높은 인식률을 보인다. 두 방식 모두 실험해보아 더 나은 방법을 선택하는게 가장 정확하겠지만 대부분 직관적으로 어떤 방법이 더 나은지 알 수 있지 않을까 생각한다.

## gradient descent

ML에서 가장 중요한 부분인 함수를 학습시키는 원리로 gradient descent가 사용된다. Cost function을 결정하여 가장 0에 가깝도록 하는 것을 목표로 매 학습 사이클마다 learning rate에 맞춰 조금씩 weight과 bias를 바꾼다. 이 때 매번 cost function의 1,2차 미분을 하는 것이 computing power를 많이 잡아먹으므로 learning cycle을 epoch 단위로 나눠 각 epoch 마다 랜덤하게 training sample을 뽑아 1,2차 미분값을 구하는 방법을 사용한다. 이를 stochastic gradient descent라 한다.
