---
layout: single
title: "[programmers Lv2] 괄호변환(C++)"
categories: 
tag: [Coding Test, programmers, C++, recursive, implementation]
toc: true
author_profile: false
search: true
---

# [programmers Lv2] 괄호변환(C++)

![문제 링크](https://programmers.co.kr/learn/courses/30/lessons/60058?language=cpp)

해당 문제는 2020 카카오 공채의 문제로 programmers 기준 Level 2의 문제이다.

사용한 알고리즘으로는 `구현(implementation)`, `재귀(recursive)` 정도가 있겠다.

저는 사실 이 문제를 적어도 3번 이상 푸는 것 같지만, 포스팅을 하는 이유는 C++ `string` 의 `substr` 메서드에 대해 내가 몰랐던 사실을 기록하기 위해서이다.

## std::string::substr

```c++
string substr (size_t pos = 0, size_t len = npos) const;
```

### parameters

`pos` : 
- Position of the first character to be copied as a substring.
- **If this is equal to the string length, the function returns an empty string.**
- If this is greater than the string length, it throws out_of_range.
- Note: The first character is denoted by a value of 0 (not 1).

pos 매개변수에 대한 사실을 처음 알았는데, **pos(부분문자열 시작 위치)가 만약 원본 문자열의 길이가 같다면, 빈 문자열(empty string)을 반환한다는 것이다!!**

EX)

```c++
#include <string>

using namespace std;

string a = "abcde";
string b = a.substr(a.length());
// b == ""
```

## 문제 풀이 코드

```c++
#include <string>
#include <vector>

using namespace std;

int isBalanced(string s);
bool isProper(string s);

string solution(string p) {
    string answer = "";

    if (p.empty()) return p;
    int index = isBalanced(p);
    string u = p.substr(0, index + 1);
    string v = p.substr(index + 1);
    if (isProper(u)) {
        return u + solution(v);
    }
    else {
        answer += "(" + solution(v) + ")";
        u.erase(u.begin());
        u.pop_back();
        for (int i = 0; i < u.length(); ++i) u[i] = (u[i] == '(') ? ')' : '(';
        answer += u;
        return answer;
    }
}

int isBalanced(string s) {
    int cnt = 0;
    for (int i = 0; i < s.length(); ++i) {
        if (s[i] == '(') ++cnt;
        else --cnt;
        if (cnt == 0) return i;
    }
    return -1;
}

bool isProper(string s) {
    int cnt = 0;
    for (int i = 0; i < s.length(); ++i) {
        if (s[i] == '(') ++cnt;
        else {
            --cnt;
            if (cnt == 0) return true;
        }
    }
    return false;
}
```


