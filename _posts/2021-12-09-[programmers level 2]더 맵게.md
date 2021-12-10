---
layout: single
title: "[programmers Lv2] 더 맵게(C++)"
categories: CodingTest
tag: [Coding Test, programmers, C++, heap, Priority Queue]
toc: true
author_profile: false
search: true
---

![더 맵게 문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42626)

## 풀이

전형적인 priority_queue 문제. 

배열에서 항상 최소값을 2개 뽑아서 새로운 값을 다시 배열에 넣어야 하는 문제다.
- 매번 배열의 최소값을 구하기 위해 `sort()` 함수를 사용할 수 없으므로
- `priority_queue` 를 사용해야 한다.

하지만, `C++` 의 prioriry_queue는 `Max Heap`이므로 `구조체(struct)` 를 선언하여 **그 구조체를 type으로 가지는 Min Heap을 선언해야 한다.**

여기서 중요한 점은 구조체를 선언할 때에 있다.
- 구조체 안의 값을 비교하는 `compare()` 함수를 선언할 때,
- 보통의 `sort()` 함수에서 사용하는 비교와 **반대로** 생각하면 쉽다!!

**아래 3개의 예시에서는 문제에서 주어진 상황과 같이 항상 최소값을 Root로 가지는 Min Heap을 선언한다.**

**EX1)**

```c++
typedef struct compare {
    bool operator () (int a, int b) const {
        return a > b;
    }
}compare; 

priority_queue<int, vector<int>, compare> PQ;
```

**EX2)**

```c++
typedef struct Node {
    int x;
}Node;

typedef struct compare {
    bool operator () (const Node& a, const Node& b) const {
        return a.x > b.x;
    }
}compare; 

priority_queue<Node, vector<Node>, compare> PQ;
```

**EX3)**

```c++
typedef struct Node {
    int x;

    bool operator < (const Node& other) const {
        return x > other.x;
    }
}Node;

priority_queue<Node> PQ;
```


## 코드

위의 3개의 예시 중 3번째 예시와 같이 구조체를 사용하여 풀이에 적용했습니다.

```c++
#include <string>
#include <vector>
#include <queue>
#include <iostream>

using namespace std;

typedef struct Node {
    int x;

    bool operator < (const Node& other) const {
        return x > other.x;
    }
}Node;

priority_queue<Node> PQ;

int solution(vector<int> scoville, int K) {
    int answer = 0;

    for (int s : scoville) {
        PQ.push({ s });
    }

    while (!PQ.empty()) {
        if (PQ.top().x >= K) break;
        if (PQ.size() < 2) return answer = -1;
        int first = PQ.top().x;
        PQ.pop();
        int second = PQ.top().x;
        PQ.pop();
        PQ.push({ first + 2 * second });
        ++answer;
    }

    return answer;
}
```