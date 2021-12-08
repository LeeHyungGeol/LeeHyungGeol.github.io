---
layout: single
title: "[programmers Lv1] 체육복(C++)"
categories: 
tag: [Coding Test, programmers, C++, Greedy]
toc: true
author_profile: false
search: true
---

# [programmers Lv1] 체육복(C++)

![문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42862)

## 풀이

전형적인 greedy(탐욕법) 문제이다. 

문제에서 요구한데로 `체육복을 안갖고 온 사람(Arr[i])` 의 
- `앞(Arr[i-1])` 에 여벌의 체육복이 있는 사람이 있다면(`Arr[i-1] > 0`), 체육복을 빌려준다.`(++Arr[i]; --Arr[i-1];)`
- 마찬가지로 `뒤(Arr[i+1])`에 여벌의 체육복이 있는 사람이 있다면(`Arr[i+1] > 0`), 체육복을 빌려준다.`(++Arr[i]; --Arr[i+1];)`    
  
### 주의할 점

저는 이 문제에서 n명의 학생이 있고, 문제에서 주어진 대로 범위를 `1 <= i <= n` 으로 잡고 문제를 풀었다.
- 그래서 for문의 범위도 `for(int i = 1; i <= n; ++i)` 으로 잡았다.

그런데 여기서 주의할 점이 i를 기준으로 `i-1, i+1` 을 탐색해야 하기 때문에
- 배열의 범위를 `n+2` 로 잡고 풀어야 모든 test case들을 맞출 수 있다.

cf) 참고로 저는 처음에 배열의 범위를 n+1 로 잡고 문제를 풀다가 9번 test case가 안풀려서 조금 고생했다.. 

## 코드

```c++
#include <string>
#include <vector>

using namespace std;

vector<int> Arr;

int solution(int n, vector<int> lost, vector<int> reserve) {
    int answer = 0;
    
    Arr.resize(n+2, 0);
    
    for(int l : lost) --Arr[l];
    for(int r : reserve) ++Arr[r];
    
    for(int i = 1; i <= n; ++i) {
        if(Arr[i] < 0) {
            if(Arr[i-1] > 0) {
                --Arr[i-1];
                ++Arr[i];
            }
            else if(Arr[i+1] > 0) {
                --Arr[i+1];
                ++Arr[i];
            }
        }
    }
    
    for(int i = 1; i <= n; ++i) {
        if(Arr[i] >= 0) ++answer;
    }
    
    return answer;
}
```