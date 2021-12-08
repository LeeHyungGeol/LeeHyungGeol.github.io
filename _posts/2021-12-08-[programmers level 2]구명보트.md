---
layout: single
title: "[programmers Lv2] 구명보트(C++)"
categories: CodingTest
tag: [Coding Test, programmers, C++, 그리디, 투 포인터]
toc: true
author_profile: false
search: true
---

# [programmers Lv2] 구명보트(C++)

![구명보트 문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42885)

## 풀이

그리디, 투 포인터 문제이다.

일단은 사람들의 몸무게가 정렬되지 않은 채로 있어서 배열을 정렬해줘야 한다.`(sort)` 그리고 보트에 태울 수 있는 사람은 무조건 `2명` 이라는 조건도 주의해야 한다. 

처음에 문제를 풀었을 때는 단순한 그리디 문제로만 생각하고, 
- 앞에서부터 차례대로 2명씩 짝지어서 몸무게가 제한보다 작으면, `(people[i] + people[i+1] <= limit)`
  - 보트 수 증가 `(++answer)`
  - 사람 몸무게를 가리키는 인덱스 증가 `(++index)`
- 이렇게만 처리하여 코드 제출하니, 50점 정도 밖에 되지 않았다.

다시 풀어보니, 이 문제는 그리디 문제이면서 `투 포인터` 문제이었다. 정렬은 그대로 해줘야 한다.
- 왼쪽과 오른쪽을 가리키는 index를 각각 설정한다. `(left = 0, right = people.size()-1)`
- 양쪽 끝의 사람 2명의 몸무게가 제한보다 작으면, `(people[left] + people[right] <= limit)`
  - 보트 수 증가, 왼쪽 인덱스 증가, 오른쪽 인덱스 감소 `(++answer; ++left; --right;)`
- 작지 않다면,
  - 오른쪽에 있는 사람만 보트에 태워야 한다. `(--right; ++answer;)`
  - **가장 작은 무게의 사람(left)과 가장 많이 나가는 사람(right)이 같이 못타는 경우에는 가장 많이 나가는 사람(right)은 무조건 보트를 혼자 타야한다.** 

## 코드

```c++
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

int solution(vector<int> people, int limit) {
    int answer = 0, left = 0, right = people.size() - 1;

    sort(people.begin(), people.end());

    while (left <= right) {
        if (people[left] + people[right] <= limit) {
            ++answer;
            --right;
            ++left;
            continue;
        }
        --right;
        ++answer;
    }

    return answer;
}
```