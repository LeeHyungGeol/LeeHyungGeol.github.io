---
layout: single
title: "[백준 실버 3(2579번)] 계단 오르기(C++)"
categories: 
tag: [Coding Test, baekjoon, 백준, 2579번, C++, DP, Dynamic Programming]
toc: true
author_profile: false
search: true
---

# [백준 실버 3(2579번)] 계단 오르기(C++)

![계단 오르기 문제 링크](https://www.acmicpc.net/problem/2579)

## 풀이

동적 계획법(Dynaminc Programming) 문제이다.

동적 계획법은 역시 점화식을 잘 세워야 문제를 풀 수 있다.
- Arr[n] = n층 계단의 점수, Dp[n] = n층 계단 까지의 최댓값
- 문제에서 **2개의 칸은 연속으로 밟을 수 있지만, 3개의 칸은 연속으로 밟지 못한다는 조건**이 있었고,
- **마지막 도착 계단은 반드시 밟아야 한다**고 했으므로,
- **N-2 번째 계단 까지의 최댓값 + N번째 계단 , N-3번째 계단까지의 최댓값 + N-1번째 계단 + N번째 계단 중 최댓값**
- `점화식` : `Dp[N] = max(Dp[N - 2] + Arr[N], Dp[N - 3] + Arr[N - 1] + Arr[N]);`

비슷한 문제로 포도주 시식 문제가 있다.

![포도주 시식 문제 링크](https://www.acmicpc.net/problem/2156)

## 코드

```c++
#include <iostream>
#include <vector>

using namespace std;

int N;
vector<int> Dp;
vector<int> Arr;

int main() {
	cin >> N;

	Arr.resize(N + 1);
	Dp.resize(N + 1);

	for (int i = 1; i <= N; ++i) {
		cin >> Arr[i];
	}

	Dp[1] = Arr[1];
	Dp[2] = Arr[1] + Arr[2];
	Dp[3] = max(Arr[1] + Arr[3], Arr[2] + Arr[3]);

	for (int i = 4; i <= N; ++i) {
		Dp[i] = max(Dp[i - 2] + Arr[i], Dp[i - 3] + Arr[i - 1] + Arr[i]);
	}

	cout << Dp[N] << '\n';

	return 0;
}
```