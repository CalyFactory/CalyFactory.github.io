---
layout: post
title: "Softmax(Multinomail classification)"
date: 2017-04-28 08:43:38
image: '/assets/img/'
description: '머신러닝'
main-class: 'ML'
color: '#1D91F5'
tags:
- Machine Learning
categories:
twitter_text:
introduction: '다양한 변수에 대한 classification'
---

이전 포스팅 [Classification](https://calyfactory.github.io/classification/)에서는

binary classification에 대해 알아봤다.

이번에는 Multinomial classification(Softmax)에 대해 알아보자

(이번 포스팅 역시, 모든 자료의 [출처](http://hunkim.github.io/ml/))

SOFTMAX 알고리즘
====

Multinomial classification은 여러가지 변수에 대해서 옳다/그르다를 한꺼번에 판단한다.

그림으로 예를 들면 다음과 같다

![post15_ABCmodule.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post15_ABCmodule.png?raw=true)

이 X는 A, B, C 각각의 모듈을 통과하며, 현재의 아이템의 A인지 아닌지, B인지 아닌지, C인지 아닌지를 알 수 있다.

ONE HOT ENCODING 방식으로 확률상 이ㄷ 아이템이 어떤 경우인지 classify하는 것이 목적이다.

이 확률분포 검증은 어떻게 진행될까?

다음 그림을 보자.

![post15_WXY.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post15_WXY.png?raw=true)

다음과 같이 행렬 곱을 통해서 A,B,C에 대한 각각의 H(x)값을 구할 수 있다.

![post15_onehotencoding.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post15_onehotencoding.png?raw=true)

그 다음, 각각의 score에 대해서 다음과 같이 softmax 함수를 통과 시키고, 확률을 통해서 현재 값을 추측가능하다.

다음은 실제값과 예측값의 차이를 구해보자

Cost function - Cross entropy
---

![post15_cross-entropy.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post15_cross-entropy.png?raw=true)

그림에서 S(y)는 예측값, L은 실제값이다.

이 공식은 다음과 같이 분리하여, 로그 그래프를 그릴 수 있다.

![post15_graph.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post15_graph.png?raw=true)

예제로 B의 값을 가지는 L행렬과 B 예측값을 대입해보면 다음과 같이, B가 맞음을 확인할 수 있다.

![post15_ex1.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post15_ex1.png?raw=true)

또 다른 예제로, 동일한 L행렬과 A 예측값을 대입해보면 다음과 같이 무한대애 수렴하여 A는 틀렸음을 확인할 수 있다.

![post15_ex2.png](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/refgjin/post15_ex2.png?raw=true)

이 cross entropy에 대한 gredient descent는 범위를 벗어나므로 설명을 생략한다.