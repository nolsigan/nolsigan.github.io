---
layout: post
date: 2016-07-02
title: Backpropagation
tags: [neural network]
---

Backpropagation(역전파)는 neural net에게 많은 가능성을 열어준 돌파구였다. Cost function의 변화값 계산 시 Weight, bias의 개수가 늘어나다보면 간단해 보이는 식도 개수에 비례하여 늘어나기 때문에 몹시 느려진다. 이 문제를 단 두 번의 forward pass 시간으로 한번의 학습을 가능하게 한게 backpropagation이다.

기본적인 알고리즘은 다음과 같다.


	1. Input x: input layer의 activation을 세팅한다.



	2. Feedforward : layer를 지나가며 activation을 계산한다.



	3. Output error : output layer의 에러를 계산한다.



	4. Backpropagate the error : layer를 역으로 순회하며 각 layer의 에러를 계산한다.



	5. Output : 계산된 에러를 통해 각 weight과 bias 값을 변화시킨다.


기본적인 Backpropagation algorithm으로도 deep learning과 같은 복잡한 neural net에선 학습 속도가 느려 이를 향상시키기 위한 여러 논문들이 존재한다. 디테일한 상황을 제외하면 지금까지 배운 것들로도 굉장히 정확도가 높은 함수들을 만들 수 있다. Handwriting recognition의 경우 95%의 정확도를 단 74줄로 구현 가능하며 오직 sigmoid neuron과 backpropagation algorithm만을 사용한 것이다. 다양한 추가 구현을 하면 99%까지 정확도를 올릴 수 있다.
