---
layout: single
title: "[C++] set, multiset"
categories: C++
tag: [C++, set, multiset]
toc: true
author_profile: false
search: true
---

## 연관 컨테이너

`연관 컨테이너(Associate Container)` : 시퀀스 컨테이너(Sequence Container)와 달리 컨테이너의 원소들을 순차적으로 삽입하지 않고, **원소 추가시, 특정 정렬 기준(default : less)에 의해 자동 정렬되는 Containter입니다.**

### 연관 컨테이너의 종류

- `set`, `multiset`, `map`, `multimap` 

### 연관 컨테이너의 특징
- 모든 연관 컨테이너는 interface(생성자, 멤버 함수, 연산자)를 동일하게 사용하고 있습니다.
- 모든 연관 컨테이너는 `노드 기반 컨테이너`이다.
- 모든 연관 컨테이너는 `균형 이진 트리`이다.
  - `균형 이진 트리`는 보통 `Red-Black Tree`이다.
- **연관 컨테이너는 균형 이진트리를 사용하므로, 원소를 빠르게 찾을 수 있습니다.(LogN)**
- key를 정렬 기준에 맞춰 균형 이진 트리에 저장하며, **Key는 삽입, 검색, 삭제 등에 모두 이용하고 변경할 수 없습니다.**
- 양방향 반복자를 지원합니다.
- 연관 컨테이너이기 때문에, 시퀀스 컨테이너에서 지원하는 다음의 함수들을 지원하지 않습니다.
  - push_back(), push_front(), pop_back(), pop_front(), front(), back()

## set

![set](https://user-images.githubusercontent.com/56071088/145531267-d2dd65d5-f7fc-49a1-a7c4-d67f9a0ebb05.png)

- **원소의 중복을 허용하지 않습니다!!**
- 따로 정렬 기준을 정해주지 않았을 때, **정렬 기준**으로 `less(오름차순)` 를 이용합니다.
- 정렬 기준이 less일 때 원소를 삽입하면, 삽입된 원소는 이진탐색트리와 같이 부모노드의 왼쪽 자식노드는 부모노드보다 크기가 작고, 부모노드의 오른쪽 자식노드는 부모노드보다 크기가 크도록 배치됩니다.
- **반복자의 탐색 순서**는 `inorder(중위순회)` 이진 트리 탐색을 사용합니다.

### set의 자주 사용되는 함수

#### 요소 삽입 및 삭제

|함수|설명|반환형|
|---|---|---|
|insert(key)| key를 삽입하며, **원소를 가리키는 반복자**와 **성공 여부의 bool 값**인 **pair 객체를 반환**한다.| `pair<set<int>::iterator, bool> pr;` |
|erase(iter)| iter가 가리키는 원소를 제거하며, 다음 원소를 가리키는 반복자를 반환한다.| `set<int>::iterator it;` |
| clear() | 모든 원소를 제거한다. | X |
| begin() | 첫 원소를 가리키는 반복자(iter)를 반환한다. | `set<int>::iterator it;` |	 
| end() | 끝을 가리키는 반복자를 반환한다. | `set<int>::iterator it;` |	 
| rbegin() | 역 순차열의 첫 원소를 가리키는 반복자를 반환한다. | `set<int>::iterator it;` |	 
| rend() | 역 순차열의 끝을 표시하는 반복자를 반환한다. | `set<int>::iterator it;` |	 
| find(key) | key의 원소의 위치를 가리키는 반복자를 반환한다. **key가 없을 경우, end()를 반환!!** | `set<int>::iterator iter;` |
| lower_bound(key) | key의 시작 구간을 가리키는 반복자를 반환한다. | `set<int>::iterator iter_lower;` |
| upper_bound(key) | key의 끝 구간을 가리키는 반복자를 반환한다. | `set<int>::iterator iter_upper;` |
| equal_range(key) | key 원소의 반복자 구간인 **pair 객체**를 반환한다. | `pair<set<int>::iterator, set<int>::iterator> pr;` |

### set 사용 예시

```c++
#include <iostream>
#include <set>

using namespace std;

int main() {
    set<int> s1;
    set<int, greater<int>> s2;
    set<int, less<int>> s3;

    s1.insert( 50 );s1.insert( 10 );s1.insert( 80 );s1.insert( 30 );s1.insert( 70 );s1.insert( 60 );

    s2.insert( 50 );s2.insert( 10 );s2.insert( 80 );s2.insert( 30 );s2.insert( 70 );s2.insert( 60 );

    s3.insert( 50 );s3.insert( 10 );s3.insert( 80 );s3.insert( 30 );s3.insert( 70 );s3.insert( 60 );


    set<int>::iterator it1;
    for( it1 = s.begin(); it1 != s.end() ; it1++)
        cout << *it1 << ' ';
    cout << '\n';
    // default : 오름차순 : 10 30 50 60 70 80

    set<int, less<int>>::iterator it2;
    for( it2 = s.begin(); it2 != s.end() ; it2++)
        cout << *it2 << ' ';
    cout << '\n';
    // less : 오름차순 : 10 30 50 60 70 80

    set<int, greater<int>>::iterator it3;
    for( it3 = s.begin(); it3 != s.end() ; it3++)
        cout << *it3 << ' ';
    cout << '\n';
    // greater : 내림차순 : 80 70 60 50 30 10

    // find(key)
    set<int>::iterator iter; 
    iter = s.find( 80 );

    if( iter != s.end() )
        cout << *iter << endl;
    else
        cout << "없음!" << endl;

    // lower_bound(key)
    set<int>::iterator iter_lower;
    iter_lower = s.lower_bound(50);

    if( iter_lower == s.end() )
        cout << "없음!" << endl;
    else
        cout <<"lower: " << *iter_lower<< endl; 
    // lower: 50

    // upper_bound(key)
    set<int>::iterator iter_upper;
    iter_upper = s.upper_bound(50);

    if( iter_upper == s.end() )
        cout << "없음!" << endl;
    else
        cout <<"upper: " << *iter_upper<< endl; 
    // upper: 60

    // equal_range(key)
    pair< set<int>::iterator, set<int>::iterator> iter_pair;
    iter_pair = s.equal_range(50);

    if( (iter_pair.first) == s.end() && (iter_pair.second == s.end()) )
        cout << "없음!" << endl;
    else
        cout << *iter_pair.first << ',' << *iter_pair.second << endl; 
    // 50, 60

    return 0;
}
```

## multiset

![multiset](https://user-images.githubusercontent.com/56071088/145531269-8c95c0f0-4d48-4d54-b2fe-b0ca2fdfcc8f.png)

- `#include < set> 의 헤더 파일` 안에 있습니다
- multiset은 set과 다르게 **중복 원소**를 컨테이너에 저장할 수 있습니다.
- multiset.insert(key) 시에,
  - set에서는 insert의 return 값이 pair 객체로 나왔지만, 
  - multiset에서는 중복이 가능하므로 insert의 return 값이 삽입한 원소 key 의 위치를 가리키는 반복자 입니다.
- set에서 의미없게 사용되었던, `lower_bound()`, `upper_bound()`, `equal_range()` 멤버 변수가 multiset에서는 좀더 유용하게 사용됩니다.

### multiset 사용 예시

```c++
#include <iostream>
#include <set>

using namespace std;

int main() {
     multiset<int> ms;

    ms.insert( 50 );ms.insert( 10 );ms.insert( 80 );ms.insert( 80 );ms.insert( 80 );ms.insert( 30 );ms.insert( 50 );ms.insert( 70 );ms.insert( 60 ;


    multiset<int>::iterator iter;
    for( iter = ms.begin(); iter != ms.end() ; iter++)
        cout << *iter << ' ';
    cout << '\n';
    // 10 30 50 50 60 70 80 80 80
    
    // lower_bound(key)
    iter=s.lower_bound(50);
    cout<<"lower_iter: "<<*iter<<endl;
    // lower_iter: 50

    // upper_bound(key)
    iter=s.upper_bound(50);
    cout<<"upper_iter: "<<*iter<<endl;
    // upper_iter: 60

    // equal_range(key)
    pair<multiset<int>::iterator, multiset<int>::iterator > iter_pair;
    iter_pair = s.equal_range( 80 );

    for( iter = iter_pair.first ; iter != iter_pair.second ; iter++)
        cout << *iter << ' ';
    cout << endl;
    // 80 80 80

    return 0;
}

```

### 참고 문헌 및 링크
- https://sy99.tistory.com/34
- https://blog.daum.net/coolprogramming/82
- https://blockdmask.tistory.com/80