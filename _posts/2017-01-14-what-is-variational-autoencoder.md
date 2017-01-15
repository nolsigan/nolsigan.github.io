---
layout: post
date: 2017-01-14
title: What is variational autoencoder?
---

> Jaan Altosaar의 블로그 글 *What is variational autoencoder* 를 허락하에 번역한 글입니다.
> 한글로 번역하기 어렵거나 영어로 두는 게 나은 단어들은 그대로 사용하였습니다.

### Variational Autoencoder(VAE) 를 두 가지 관점에서 이해하기: 딥러닝과 그래프 모델

딥러닝 연구자와 확률론적 기계 학습 연구자는 왜 variational autoencoder(VAE) 를 논의할 때 혼란스러워 하는가?
VAE 란 무엇인가? 왜 이 용어를 둘러싼 많은 혼란이 있을까?

개념과 언어간의 거리가 있기 때문이다. 뉴럴넷의 과학과 확률 모델은 같은 언어를 사용하지 않는다.
나의 목표는 이 거리를 좁히고 두 분야간의 협력과 토의가 활발해질 수 있도록 하고 이를 구현한 코드를 제공하는 것이다. ([Github link](https://github.com/altosaar/vae/blob/master/vae.py))

Variational Autoencoder 는 멋지다. 이 모델은 복잡한 generative model 를 설계하고 큰 데이터셋에 적용할 수 있게 해준다. 또한 허구의 연예인 얼굴과 높은 화질의 디지털 작품을 생성할 수도 있다.

<figure>
    <img src="{{ '/assets/img/variational-autoencoder-faces.jpg' | prepend: site.baseurl }}" alt="">
    <figcaption>VAE 로 생성된 허구의 연예인 얼굴들 (<a href="https://www.youtube.com/watch?v=XNZIN7Jh3Sg">by Alec Radford</a>)</figcaption>
</figure>

이 모델은 또한 image generation 과 reinforcement learning 에서 최고의 결과(state-of-the-art)를 얻는다.
VAE 는 2013년에 [Kingma et al.](https://arxiv.org/abs/1312.6114) 과 [Rezende et al.](https://arxiv.org/abs/1401.4082) 에 의해 정의되었다.

VAE 를 설명하기 위한 언어를 어떻게 만들 수 있을까? 먼저 뉴럴넷의 관점에서 생각해보고, 그래프 모델의 variational inference 를 통해 생각해보자.

#### 뉴럴넷 관점

뉴럴넷 관점에서 VAE 는 encoder, decoder 그리고 loss function 으로 구성된다.

<figure>
    <img src="{{ '/assets/img/encoder-decoder.png' | prepend: site.baseurl }}" width="70%" alt="">
    <figcaption>Encoder 는 데이터를 잠재된 차원 (z) 로 압축한다. Decoder 는 잠재된 차원 (Z) 로부터 데이터를 재구성한다.</figcaption>
</figure>

Encoder 는 뉴럴넷이다. 인풋은 datapoint $$ x $$ 이고, 아웃풋은 잠재된 차원(hidden representation) $$ z $$ 이며, weight 들과 biase $$ \theta $$ 를 가지고 있다.
구체적으로 설명하기 위해, $$ x $$ 를 28 x 28 사이즈의 손으로 쓴 숫자 사진이라고 생각하자.
Encoder 는 784 차원 데이터를 훨씬 낮은 차원의 잠재된 차원 $$ z $$ 로 암호화(encode)한다.
이는 흔히 병목 지역이라 불리는데, encoder 가 데이터를 낮은 차원으로 효율적으로 압축하는 것을 배워야하기 때문이다.
Encoder 를 $$ q_\theta (z \vert x) $$ 로 표현하자. 낮은 차원 ($$ z $$)이 확률론적임을 확인하자: 
encoder 는 Gaussian probability density 인 $$ q_\theta (z \vert x) $$ 에 파라미터를 아웃풋으로 내보낸다.
우리는 이 확률 분포에서 샘플을 뽑음으로써 $$ z $$ 에 잡음이 포함된 값을 얻을 수 있다.

Decoder 는 또 다른 뉴럴넷이다. 인풋은 $$ z $$ 이고 아웃풋은 데이터 확률분포의 파라미터 값들이며 weight 과 biase $$ \phi $$ 를 갖는다.
Decoder 는 $$ p_\phi (x \vert z) $$ 로 표현된다. 손으로 쓴 숫자 사진을 사용한다고 할 때, 사진은 흑백이고 각 픽셀이 0 이나 1 이라 하자.
그러면 픽셀 하나의 확률분포는 Bernoulli distribution 으로 표현할 수 있다.
Decoder 는 숨겨진 차원 $$ z $$ 을 input 으로 받아 784 개의 각각 이미지 픽셀에 대한 Bernoulli 파라미터들을 output 으로 내보낸다.
실수로 된 $$ z $$ 를 784 개의 0 과 1 사이의 실수로 해독(decode)하는 것이다.
낮은 차원에서 높은 차원으로 가기 때문에 정보의 손실이 발생한다. 얼마나 손실이 발생하는가?
우리는 이 것을 단위가 자연수인 reconstruction log-likelihood $$\log p_\phi (x\vert z)$$ 를 사용하여 측정한다.
이 측정값이 decoder 가 숨겨진 차원 $$ z $$ 에서 인풋 $$ x $$ 를 얼마나 효율적으로 재생성 했는지를 알려준다.

VAE 의 loss function 은 regularizer 가 포함된 negative log-likehood 이다.
모든 datapoint 가 공유하는 전역 변수가 없으므로 우리는 loss function 을 하나의 datapoint $$ l_i $$ 에만 의존적이도록 나눌 수 있다.
총 loss 는 총 N 개의 datapoint 에 대해 $$ \sum_{i=1}^N l_i $$ 가 된다.
Datapoint $$ x_i $$ 에 대한 loss function $$ l_i $$ 는 다음과 같다.

$$ l_i(\theta, \phi) = - E_{z\sim q_\theta(z\vert x_i)}[\log p_\phi(x_i\vert z)] + KL(q_\theta(z\vert x_i) \vert\vert p(z))$$

앞 부분은 재생성 loss 혹은 i 번째 datapoint 의 negative log-likelihood 기대값이다.
기대값은 encoder 의 분포와 관련 있다. 이 부분이 decoder 를 데이터를 재생성하도록 유도한다.
Decoder 의 output 이 데이터를 잘 재생성하지 못한다면 loss function 이 높은 값을 갖게 된다.

뒷 부분은 우리가 추가하는 regularizer 이다 (유도하는 법은 나중에 나온다).
이 것은 encoder 의 분포 $$ q_\theta(z\vert x) $$ 와 $$ p(z) $$ 사이의 Kullback-Leibler divergence 이다.
이 divergence 는 $$ q $$ 를 사용하여 $$ p $$ 를 표현할 때 얼마나 정보가 유실 되는지를 측정한다.
$$ q $$ 가 $$ p $$ 에 얼마나 가까운 지를 측정하는 방법 중 하나이다.

VAE 에서 $$ p $$ 는 평균 0 과 분산 1 을 갖는 표준정규분포 $$ p(z) = Normal(0, 1) $$ 이다.
Encoder 가 표준정규분포와 다른 $$ z $$ 를 output 으로 갖는다면 loss 값에서 손해(penalty)를 본다.
이 regularizer 는 $$ z $$ 의 각 숫자를 충분히 다양하도록 만들라는 뜻이다.
우리가 regularizer 를 포함시키지 않는다면 encoder 는 각 datapoint 에 대해 유클리드 공간상의 다른 영역을 나타내도록 편법을 사용할 수도 있다.
이것은 좋지 않은 현상인데, 똑같은 숫자를 나타내는 두 이미지 (두 명이 쓴 숫자 2, $$ 2_{alice} $$ 와 $$ 2_{bob} $$) 가 각각 다른 값 $$ z_{alice} $$ 와 $$ z_{bob} $$ 을 가질 수 있기 때문이다.
우리는 $$ z $$ 값이 의미 있길 원하기 때문에 이 행동을 제한한다.
이를 통해 비슷한 숫자의 표현을 가깝게 유지할 수 있다.
(e.g. 따라서 $$ z_{alice} $$, $$ z_{bob} $$, $$ z_{ali} $$ 값이 충분히 가깝게 유지된다)

#### 확률 모델의 관점

이제 VAE 를 확률 모델의 관점에서 생각해보자. 딥러닝과 뉴럴넷에 대한 지식을 잠시만 잊어버리자.
뉴럴넷과 분리시켜야 다음 개념들을 좀 더 선명하게 볼 수 있을 것이다.
우리는 마지막에 다시 뉴럴넷을 상기할 것이다.

확률 모델의 관점에서 VAE 는 어떤 특정한 확률 모델을 갖는 data $$ x $$ 와 숨겨진 변수(latent variable) $$ z $$ 를 갖는다.
우리는 이 모델의 joint probability 를 $$ p(x, z) = p(x\vert z)p(z) $$ 로 표현할 수 있다.
생성 과정은 다음과 같다.

각 datapoint $$ i $$ 에 대해:

* 숨겨진 변수 $$ z_i \sim p(z) $$ 를 그린다.
* datapoint $$ x_i \sim p(x\vert z) $$ 를 그린다.

이 것을 그래프 모델로 표현하면:

<figure>
    <img src="{{ '/assets/img/graphical-model-variational-autoencoder.png' | prepend: site.baseurl }}" width="30%" alt="">
    <figcaption>VAE 의 그래프 모델. 숨겨진 변수 z 는 표준정규분포이며 data 는 P(X|Z) 에서 추출된다. 그림자 진 노드 X 는 관찰된 data 를 의미한다. 손글씨 숫자 이미지에 대해서 이 data likelihood 는 Bernoulli distribution 을 따른다.</figcaption>
</figure>

이 모델이 우리가 VAE 를 확률 모델의 관점에서 생각할 때 가장 중요한 부분이다.
숨겨진 변수는 prior $$ p(z) $$ 로부터 얻어진다.
데이터 $$ x $$ 는 숨겨진 변수 $$ z $$ 를 조건부로 한 likelihood $$ p(x\vert z) $$ 를 갖는다.
모델은 data 와 숨겨진 변수에 대한 joint probability $$ p(x, z) $$ 를 정의한다.
우리는 이를 likelihood 와 prior 로 분해할 수 있다: $$ p(x, z) = p(x\vert z)p(z) $$
흑백 숫자 이미지의 경우, likelihood 는 Bernoulli distribution 을 따른다.

이제 우리는 이 모델에서의 inference 에 대해 생각해 볼 수 있다.
목표는 관찰된 데이터로부터 숨겨진 변수를 잘 추론(infer)하는 것이다.
곧, posterior $$ p(z\vert x) $$ 를 계산하는 것과 일치한다.
Bayes 에 따르면:

$$ p(z\vert x) = \frac{p(x\vert z)p(z)}{p(x)}. $$

분모 $$ p(x) $$ 를 생각해보자. 우리는 이를 evidence 라 부르고 숨겨진 변수를 제거하여 얻을 수 있다: $$ p(x) = \int p(x, z)p(z)dz $$.
운이 없게도, 이 적분을 계산하기 위해선 모든 숨겨진 변수의 가능성을 고려해야하기 때문에 지수 시간이 걸린다.
따라서 우리는 이 posterior distribution 을 근사해야한다.

Variational inference 가 $$ q_\lambda(z\vert x) $$ 계열의 분포를 근사할 수 있다.
Variational parameter $$ \lambda $$ 는 분포 계열을 구별할 수 있게 한다.
예를 들어, $$ q $$ 가 Gaussian 이라면, 각 datapoint $$ x_i $$ 에 대한 숨겨진 변수의 평균과 분산이 된다. $$ \lambda_{x_i} = (\mu_{x_i}, \sigma^2_{x_i}) $$

우리의 variational posterior $$ q(z\vert x) $$ 가 진짜 posterior $$ p(z\vert x) $$ 를 얼마나 잘 근사하는지 어떻게 알 수 있을까?
우리는 $$ q $$ 로 $$ p $$ 를 근사할 때 정보 손실을 측정하는 Kullback-Leibler divergence 를 사용한다.

$$ 
KL(q_\lambda(z \vert x) \vert \vert p(z \vert x)) = 
$$

$$
\mathbf{E}_q[\log q_\lambda(z \vert x)]- \mathbf{E}_q[\log p(x, z)] + \log p(x)
$$

우리의 목표는 divergence 를 최소화하는 $$ \lambda $$ 를 찾는 것이다.
따라서 최적의 근사 posterior 는 다음과 같다.

$$ q_\lambda^* (z \vert x) = {\arg\min}_\lambda KL(q_\lambda(z \vert x) \vert \vert p(z \vert x)). $$

왜 이 값을 바로 계산하는게 불가능한가? 귀찮은 evidence $$ p(x) $$ 가 식에 나타나기 때문이다.
위에서 논의했듯이 이 값은 다루기가 매우 힘들다. 다룰 수 있는 variational inference 를 위해선 하나의 조건이 더 필요하다.
아래의 함수를 봐보자:

$$ ELBO(\lambda) = \mathbf{E}_q[\log p(x, z)] - \mathbf{E}_q[\log q_\lambda(z \vert x)]. $$

이 식을 Kullback-Leibler divergence 와 합치고 다음과 같이 evidence 를 다시 쓸 수 있음을 확인하자.

$$ \log p(x) = ELBO(\lambda) + KL(q_\lambda(z \vert x) \vert \vert p(z \vert x)) $$

Jensen's inequality 에 의해, Kullback-Leibler divergence 는 항상 0 보다 크거나 같다.
이는 곧 Kullback-Leibler divergence 를 최소화하는 것이 ELBO 를 최대화하는 것과 동치임을 말한다.
요약하자면 Evidence Lower BOund (ELBO) 가 posterior inference 근사를 가능하게 한다.
우리는 더이상 Kullback-Leibler divergence 를 최소화하기 위해 시간을 쓸 필요가 없다.
대신, 우리는 ELBO 를 최대화하므로써 계산 가능한 동치의 일을 수행한다.

VAE 에선 오직 local latent variable 만이 존재한다 (서로 다른 두 datapoint 가 latent $$ z $$ 를 공유하지 않는다).
따라서 우리는 ELBO 를 단일 datapoint 단위의 합으로 계산한다.
이를 통해 우리는 $$ \lambda $$ 에 대해 stochastic gradient descent 를 사용할 수 있다.
VAE 에서 단일 datapoint 에 대한 ELBO 는 다음과 같다.

$$ ELBO_i(\lambda) = E_{q_\lambda(z\vert x_i)}[\log p(x_i\vert z)] - KL(q_\lambda(z\vert x_i) \vert\vert p(z)). $$

이 값이 예전 ELBO 의 정의와 같은 것을 보이기 위해선 log joint 값을 prior 와 likelihood 값으로 확장하고
log 의 곱셈 법칙을 사용하면 된다.

이제 뉴럴넷 관점과 연결해보자.
마지막 과정은 근사 posterior $$ q_\theta(z\vert x, \lambda) $$ 를 데이터 $$ x $$ 를 인풋으로 받고 파라미터 $$ \lambda $$ 를 아웃풋으로 내보내는 inference network(혹은 encoder) 로 파라미터화 하는 것이다.
또한 likelihood $$ p(x\vert z) $$ 를 latent variables 를 인풋으로 받고 데이터 분포 $$ p_\phi(x\vert z) $$ 의 파라미터를 아웃풋으로 내보내는 generative network(혹은 decoder) 로 파라미터화 한다.
Inference 와 generative network 는 각각 파라미터 $$ \theta $$ 와 $$ \phi $$ 를 갖는다.
파라미터들은 뉴럴넷 상에서 weight 과 biase 가 된다.
우리는 stochastic gradient descent 를 통해 이를 최적화하여 ELBO 를 최대화한다. (global latent variable 이 없기 때문에 데이터를 minibatch 로 다루는 것이 더 정결하다)
ELBO 에 inference 와 generative network 의 파라미터를 더해서 쓰면 다음과 같다.

$$ ELBO_i(\theta, \phi) = E_{q_\theta(z\vert x_i)}[\log p_\phi(x_i\vert z)] - KL(q_\theta(z\vert x_i) \vert\vert p(z)). $$

이 evidence lower bound 는 VAE 의 loss function 의 역으로써, 우리가 뉴럴넷 관점에서 논의한 내용이다; $$ ELBO_i(\theta. \phi) = -l_i(\theta, \phi) $$.
우리는 원리를 통해 이 결론에 도달하기 위해 확률 모델과 approximate posterior inference 를 사용하였다.
우리는 여전히 Kullback-Leibler divergence 를 regularizer 관점, 또는 재생성 손실 관점에서 likelihood 기대값으로 생각할 수 있다.
하지만 확률 모델을 통한 설명이 왜 이 부분이 존재하는 지를 분명하게 보여준다: approximate posterior $$ q_\lambda(z\vert x) $$ 와 posterior $$ p(z\vert x) $$ 사이의 Kullback-Leibler divergence 를 최소화하는 일.

이게 전부다! 우리는 확률 모델, 목표 함수(ELBO), 그리고 inference 알고리즘(ELBO 의 gradient ascent)을 정의하였다.

### Experiments

이제 우리는 모델의 샘플들을 볼 준비가 되었다.
진행과정을 측정하는 두 가지 방법으로 prior 혹은 posterior 에서 샘플링하는 방법이 있다.
학습된 숨겨진 차원(latent space)를 더 잘 이해하기 위해 숨겨진 변수 $$ q_\lambda(z\vert x) $$ 의 posterior distribution 을 시각화 할 수 있다.

계산적 관점에서, 이 것은 곧 정규분포의 변수를 얻기 위해 인풋 이미지 $$ x $$ 를 inference network 에 넣어주고 숨겨진 변수 $$ z $$ 에서 샘플을 얻는 것을 의미한다.
Inference network 이 어떻게 더 좋은 approximate posterior distribution 를 찾는 지 확인하기 위해 학습 과정에서 그래프에 기록하고 서로 다른 숫자에 대한 숨겨진 변수를 서로 다른 부분의 숨겨진 차원에 배치한다.
학습을 시작한 시점에는 숨겨진 변수의 분포가 prior 와 비슷함을 확인하자 (0 근처의 둥근 방울).

<center>
<iframe src="//giphy.com/embed/26ufoVqZDjHoPrp8k?html5=true" width="480" height="413" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>
</center>

<figure>
    <figcaption>학습 과정에서 approximate posterior 의 시각화. 학습이 진행됨에 따라 숫자 클래스들이 2차원 숨겨진 차원으로 구별된다.</figcaption>
</figure>

우리는 또한 prior predictive distribution 을 시각화할 수 있다. 우리는 숨겨진 변수가 $$ -3 $$ 과 $$ 3 $$ 사이에 같은 간격으로 놓이도록 값을 수정한다.
그 다음 generative network 에 의해 파라미터화 된 likelihood 에서 샘플을 추출한다.
이 '환각을 일으키는'(hallucinated) 이미지들은 각 숨겨진 차원의 부분에 대해 모델이 어떻게 연상하는지를 보여준다.

<center>
<iframe src="//giphy.com/embed/26ufgj5LH3YKO1Zlu?html5=true" width="480" height="480" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>
</center>

<figure>
    <figcaption>likelihood 의 샘플을 통한 prior predictive distribution 의 시각화. X 와 Y 축은 -3 과 3 사이에 같은 간격으로 놓여진 숨겨진 변수이다.</figcaption>
</figure>

### 용어 사전

우리는 명확하고 간결하게 VAE 를 설명하기 위한 언어를 선택해야한다. 다음은 몇 개의 내가 헷갈렸던 내용에 대한 용어사전이다.

* __Variational Autoencoder (VAE)__: 뉴럴넷의 언어에서 VAE 는 encoder, decoder, loss function 으로 구성된다.
확률 모델의 관점에서의 VAE 는 approximate posterior 와 model likelihood 가 뉴럴넷(inference 와 generative networks)에 의해 파라미터화 된 latent Guassian model 에서의 approximate inference 를 의미한다.
* __Loss function__: 뉴럴넷의 언어에서 우리는 loss function 을 생각한다. 학습은 이 loss function 을 최소화하는 일이다.
하지만 variational inference 에선 loss function 이 아닌 $$ ELBO $$ 를 최대화한다.
이는 뉴럴넷 프레임워크들이 최소화만 지원해서 `optimizer.minimize(-elbo)` 와 같은 어색한 방법을 쓰게 한다.
* __Encoder__: 뉴럴넷 세계에서 encoder 는 데이터 $$ x $$ 에 대한 표현 $$ z $$ 를 내보내는 뉴럴넷이다.
확률 모델 관점에서 __inference network__ 는 숨겨진 변수 $$ z $$ 의 approximate posterior 를 파라미터화 한다.
Inference network 은 분포 $$ q(z\vert x) $$ 에 파라미터를 아웃풋으로 내보낸다.
* __Decoder__: 딥러닝에서 decoder 는 $$ z $$ 가 주어졌을 때 데이터 $$ x $$ 를 재생성하는 법을 배우는 뉴럴넷이다.
확률 모델의 관점에선 숨겨진 변수 $$ z $$ 가 주어졌을 때 데이터 $$ x $$ 의 likelihood 은 __generative network__ 에 의해 파라미터화 된다.
Generative network 는 likihood distribution $$ p(x\vert z) $$ 에 파라미터를 아웃풋으로 내보낸다.
* __Local latent variables (지역 숨겨진 변수)__: 이것은 각 datapoint $$ x_i $$ 에 대한 $$ z_i $$ 이다.
Global latent variable 은 존재하지 않는다. Local latent variable 만 존재하기 때문에 우리는 쉽게 ELBO 를 단일 datapoint $$ x_i $$ 에만 의존적인 $$\mathcal{L}_i$$ 로 분해 가능하다.
이는 곧 stochastic gradient descent 를 가능하게 한다.
* __Mean-field versus amortized inference__: Mean-field variational inference 에선 각 datapoint $$ \lambda_i $$ 에 대한 파라미터들이 존재한다 (e.g. Gaussian latent variable 의 $$ \lambda_i = (\mu_i, \sigma_i) $$).
VAE 에선 전역 파라미터들(이 경우엔 inference network 의 파라미터 $$ \theta $$)이 존재하는 __amortized inference__ 를 사용한다.
이 전역 파라미터들은 모든 datapoint 에서 공유한다.
* __Inference__: 뉴럴넷에서 inference 는 보통 한번도 보지 못한 datapoint 에 대한 숨겨진 차원을 추측하는 것을 의미한다.
확률 모델에선 관측된 데이터에 대해 숨겨진 변수 값을 추측하는 것을 의미한다.

### Sample implementation

이 글에 쓰인 결과들을 생성하기 위해 사용한 코드를 공유한다: [Github link](https://github.com/altosaar/vae/blob/master/vae.py)

### 각주: 재파라미터화(reparametrization) 요령

마지막으로 우리가 VAE 에 구현해야할 것은 어떻게 통계학적 변수로부터 도함수를 구할 것인가이다.
우리가 $$ q_\theta(z\vert x) $$ 로부터 $$ z $$ 를 받았고 $$ \theta $$ 에 대한 $$ z $$ 의 도함수를 알고 싶을때 어떻게 할 것인가?
샘플 $$ z $$ 는 고정값이지만, 우리는 본능적으로 이것의 도함수가 0이 아님을 알 수 있다.

몇몇 분포에 대해선 샘플을 영리하게 재파라미터화하여 확률성이 파라미터와 독립적으로 만들 수 있다.
우리는 샘플들이 결정론적으로 분포의 파라미터에 의존적이길 원한다.
예를 들어, 평균 $$ \mu $$ 와 분산 $$ \sigma $$ 를 가지는 정규분포 변수에 대해 우리는 다음과 같이 샘플을 뽑을 수 있다.

$$ z = \mu + \sigma \odot \epsilon, (\epsilon \sim Normal(0, 1))$$

$$ \sim $$ 에서 $$ = $$ 로 부호가 바뀌는 것은 매우 중요한 과정이다.
우리는 파라미터에 대해 결정론적으로 의존적인 함수를 정의하였다.
따라서 우리는 $$ z $$, $$ f(z) $$ 를 포함하고 $$ \mu $$ 와 $$ \sigma $$ 를 파라미터로 갖는 함수의 도함수를 얻을 수 있다.

<figure>
    <img src="/assets/img/reparametrization.png">
    <figcaption>재파라미터화는 정규분포를 따르는 랜덤 변수 z 의 랜덤성을 정규분포에서 샘플로 얻은 epsilon 으로 보낸다. 다이아몬드는 결정론적인 의존관계를 의미하고 원은 랜덤 변수를 의미한다.</figcaption>
</figure>

VAE 에서 평균과 분산은 우리가 최적화하는 파라미터 $$ \theta $$ 를 가지는 inference network 의 아웃풋이다.
재파라미터화는 latent variables $$ z $$ 의 샘플들의 함수인 $$ ELBO $$ 를 통해 $$ \theta $$ 에 대한 backpropagate 을 가능하게 한다.

### 아이디어와 도표에 대한 레퍼런스

많은 아이디어와 도표는 Shakir Mohamed 의 완벽한 블로그 글 [재파라미터화](http://blog.shakirm.com/2015/10/machine-learning-trick-of-the-day-4-reparameterisation-tricks/)와 [autoencoders](http://blog.shakirm.com/2015/03/a-statistical-view-of-deep-learning-ii-auto-encoders-and-free-energy/)로부터 얻었다.
Durk Kingma 는 [재파라미터화](http://dpkingma.com/?page_id=277)에 대한 멋진 시각화를 만들었다.
Variational inference 에 대한 좋은 레퍼런스는 이 [튜토리얼](https://arxiv.org/abs/1601.00670)과 David Blei 의 [course notes](https://www.cs.princeton.edu/courses/archive/fall11/cos597C/lectures/variational-inference-i.pdf) 이다.
Dustin Tran 의 [variational autoencoder](http://dustintran.com/blog/denoising-criterion-for-variational-auto-encoding-framework/) 에 대한 블로그 글도 도움이 된다.
첫 부분의 MNIST gif 는 [Rui Shu](https://github.com/RuiShu/variational-autoencoder) 로부터 얻었다.

Thanks to Rajesh Ranganath, Ben Poole, Cassandra Xia, and Ryan Sepassi for discussions and many concepts in this article.

논의는 [Hacker News](https://news.ycombinator.com/edit?id=12292576) 와 [Reddit](https://www.reddit.com/r/MachineLearning/comments/4xv5b5/explainer_of_variational_autoencoders_from_a/) 에서 찾아볼 수 있다.