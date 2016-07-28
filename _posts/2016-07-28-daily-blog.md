---
layout: post
date: 2016-07-28
title: daily blog 07/28
tags: [daily blog, algorithm, TDD]
---

## Algorithm

### 분할 정복

가장 유명한 알고리즘 디자인 패러다임, 한 마디로 설명하면 각개 격파!

총 3단계로 알고리즘을 정의할 수 있다.

1. 문제를 더 작은 문제로 분할하는 과정 (divide)
2. 각 문제에 대해 구한 답을 원래 문제에 대한 답으로 병합 (merge)
3. 더이상 답을 분할하지 않고 곧장 풀 수 있는 매우 작은 문제 (base case)

시간복잡도는 divide, merge에 지배적이다.

ex ) merge sort, quick sort, 카라츠바의 빠른 곱셈 알고리즘


### 문제 풀이 팁

string이나 배열을 다루며 재귀 호출을 통해 문제를 해결하려 할 때, 함수에 필요한 범위만큼 잘라 인자로 넣는 방법도 있지만  
그보다 iterator를 사용해 필요한 만큼 가져다 쓰면 깔끔하게 코드를 작성할 수 있다.

Thread나 process 기반 concurrency issue가 없기 때문에 재귀 호출을 하더라도 iterator는 하나씩 전진한다는 사실을 기억하자.


## TDD

### TDD 란?

Test-Driven Development 의 약자.  
테스트 작성 - 코드 작성 - 리팩토링 의 사이클을 매우 짧게 반복하여 개발하는 프로세스.  
'테스트를 어떻게 할 것인가?' 를 생각하므로서 코드 구조에 대해 고민하고 버그 발생 확률을 낮춘다.

TDD가 쓸데없는 오버헤드가 아니라 실용적이려면 단순히 테스트를 작성하는게 아니라 사고방식을 테스트로부터 시작하는 법을 익혀야겠다는 생각이 든다.  
여러 방식을 직접 적용해보면서 점점 발전시켜 나갈 계획이다.

### Android App 에서의 TDD

Android App 처럼 UI 중심의 프로젝트는 TDD를 적용시키기가 까다로운 듯 하다.  
특히 지금 작업 중인 앱의 경우 비즈니스 로직이 서버에서 거의 다 이루어지고 있어 UI Testing 외에는 할 수 있는 테스트가 적다.

하지만 B2C 서비스의 경우, UI가 자주 바뀌기 때문에 테스트의 재활용성이 너무 낮을 뿐더러 오버헤드가 너무 크다고 느꼈다.  
그러면 유일하게 테스트가 필요한 곳은 'API 통신 후 데이터에 반영이 잘 됐는가', '데이터에 의한 process decision'이 남는다.

이 두 영역에 대한 TDD 실험을 진행하려 한다. TDD를 실제로 진행해보는게 처음이라 여러가지 실험을 해보면서 어떤 방법이 효율적인지 배우는 시간이 필요하다.

다음과 같은 Testing Tool을 쓰려한다.

* JUnit
* Mockito
* Robolectric
* Mock Web Server

기본적으로 JUnit을 통해 테스트를 작성하고 서버사이드는 Mocking하고 Robolectric을 통해 View와의 interaction을 확인하려한다. 

