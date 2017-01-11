---
layout: post
title: "EditDistance"
date: 2017-01-11 21:36:52
image: '/assets/img/'
description: 'Edit Distance Algorithm'
main-class: 'Algorithm'
color: '#B31917'
tags: 
- 'Algorithm'
categories:
twitter_text:
introduction:
---


## EditDistance
두 문장의 유사도를 구하려면 어떻게 해야 할까?

두 문장의 차이를 계산해 길이에 비례해 유사도를 구할 수 있을것이다.

그렇다면 두 문장의 차이를 어떻게 계산 할 수 있을까?

데이터가 숫자나 벡터일 경우 차이를 쉽게 계산 할 수 있지만 문자열의 경우는 힘들다.

그것을 계산하는 방식이 두 문장의 **'거리'**를 계산하는 **edit distance** 알고리즘이다.

두 문자열이 얼마나 차이나는지를 계산하기 위해 

한 문자열에서 다른 문자열로 변환되려면 얼마나 많은 '삽입', '수정', '삭제'가 필요한지 최소 수를 계산하게된다.

예를들어 terminal 과 terminator 두 문장의 거리는 

l -> t(수정), o(삭제), r(삭제)로 distance = 3이다.

그렇다면 어떻게 최소의 distance를 계산할 수 있을까?

![img](https://wikimedia.org/api/rest_v1/media/math/render/svg/1deeeaebff36dc4bdc79778bcafe0ec17ce63f83)

위 그림과 같은 간단한 2차원 다이나믹식으로 O(n^2) 내에 두 문장간 distance를 구할 수 있다.

간단한 python 예제를 살펴보자



{% highlight python %}
def get_edit_distance(string1, string2):

    s1 = split_character(string1)
    s2 = split_character(string2)

    d = [[0 for col in range(len(s2) + 1)] for row in range(len(s1) + 1)]

    for i in range(0, len(s1) + 1):
        d[i][0] = i

    for i in range(0, len(s2) + 1):
        d[0][i] = i

    for i in range(1, len(s1) + 1):
        for j in range(1, len(s2) + 1):
            if s1[i - 1] == s2[j - 1]:
                d[i][j] = d[i - 1][j - 1]
            else:
                d[i][j] = min([d[i - 1][j - 1] + 1, d[i][j - 1] + 1, d[i - 1][j] + 1])

    return d[len(s1)][len(s2)]
{% endhighlight %}











