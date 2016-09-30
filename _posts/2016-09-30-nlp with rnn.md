---
layout: post
date: 2016-09-30
title: Natural language processing with RNN
tags: [neural network, NLP]
---

## NLP ?

NLP 란 인간이 발화하는 언어 현상을 기계적으로 분석해서 컴퓨터가 이해할 수 있는 형태로 만드는 자연 언어 이해 혹은 그러한 형태를 다시 인간이 이해할 수 있는 언어로 표현하는 제반 기술을 의미한다. (WIKI)

언어의 특성을 분석하여 불변 알고리즘으로 처리하는 방법이 있고, 머신 러닝을 사용하되 아웃풋을 정해진 데이터에서 고르는 Statistical NLP 방법이 있고 마지막으로 RNN이나 DNN, 강화학습 등을 통해 아웃풋을 만드는 Generative Model 이 있다. 현재 state of the art 는 대부분 statistical NLP가 차지하고 있지만 기존 인풋들과 다른 형식의 인풋이 들어오면 아웃풋이 전혀 상관없는 값으로 튀게 되는 문제 등이 있다. Generative model은  최근 들어 computing power, deep learning의 발전과 LSTM, GRU 등 알고리즘의 발전이 이루어지면서 state of the art에 매우 근접한 성능을 내고 있으며 많은 주목을 받고 있다.

## RNN ?

Recurrent neural network는 기존 neural net의 고정 인풋 사이즈에 따른 고정 아웃풋 사이즈를 내뱉어야만 하는 한계를 극복하기 위해 만들어졌다. 뉴런들이 인풋 데이터 뿐만 아니라 이전 아웃풋까지 학습에 사용하므로서 단일 단계로 끝나는 것이 아니라 인풋이 끝날 때까지 단계를 반복한다. 가장 간단한 모형이 rnn은 n개의 토큰으로 이루어진 인풋이 들어온다면 n개의 아웃풋 토큰을 가지게 된다. 여러 단계를 거쳐 한 학습이 끝남에도 불구하고 weight과 bias를 공유하므로 기존 dnn 과 computing cost가 별 차이가 없다.

임의의 n개의 인풋 토큰에 대해 임의의 m개의 아웃풋 토큰을 뱉고 싶을 때를 위해 seq2seq model 등의 알고리즘이 연구되었지만 기본 rnn을 n,m의 최댓값을 정하고 더 짧은 인풋, 아웃풋에 대해 빈칸을 특정 토큰으로 채우는 방식으로도 대처할 수 있다.

## LSTM, GRU, ...

기존 rnn의 가장 큰 문제 중 하나는 긴 인풋 사이즈에 취약하다는 것이다. 인풋 사이즈가 길어질수록 기억해야할 정보가 많아지는데 간단한 모형으로는 그게 쉽지 않았다. 그에 따라 LSTM, GRU 등의 좀 더 복잡한 형태의 hidden layer를 사용하는 알고리즘들이 개발되었고 좋은 성과를 보이고 있으며 최근 논문에서는 대부분 당연히 사용하는 추세이다.

## seq2seq Model

기존 rnn의 또 다른 문제점 중 하나는 인풋을 끝까지 보지 못하고 각 단계에 아웃풋을 출력해야 했다는 점이다. 이를 극복하고 더 나아가 아예 인풋을 받는 encoder와 아웃풋을 내뱉는 decoder를 분리하여 두개의 rnn 모델을 사용하는 방법이 seq2seq model이다. 구글에 의해 제안되었으며 state of the art에 매우 근접한 성과를 내었다. Encoder, decoder를 나눠 변수가 두배나 많아졌지만 사실 더 좋은 성과를 내기 위해 많은 메모리, computing power를 사용하는 것은 불가피한 일인 것 같다. Decoder는 END 토큰이 나올 때까지 아웃풋을 출력한다.

seq2seq model의 중요한 특징 중 한가지는 beam search decoder를 사용했다는 점인데 하나의 아웃풋을 출력하는 것이 아니라 각 단계에 Top n개의 아웃풋을 출력하므로서 대략 n^(아웃풋 길이)의 아웃풋에서 가장 확률이 높은 것을 선택하게 된다. 이 방법으로 이미 구글 메일에서 스마트 리플라이 기능을  지원하고 있으니 참 대단하다.

Beam search decoder를 배우면서 느꼈던 의문점은 짧은 문장이 높은 확률을 가질 수 밖에 없는 구조이지 않은가였는데 인풋 길이가 길면 end 토큰이 나오는 시간도 늦춰지고 beam search length normalization을 통해 길이를 조정하는 것 같다.

## attention

Attention model은 메모리를 더 활용하여 사람처럼 특정 상황에 어떤 부분에 집중해야하는지를 기억하게 하는 방법이다. 각 인풋 토큰에 대해 attention variable이 존재하여 한 문장에서 어떤 부분에 집중해야 하는지를 각 아웃풋 토큰에 알려준다. 이 방법 또한 제안된 이후로 좋은 성과를 내고 있어 거의 모든 논문에 당연하게 사용되고 있다.