---
title: "접미사 배열(Suffix Array) 및 LCP(Longest Common Prefix)"
date: 2025-03-09
categories: [알고리즘, 문자열]
tags: [문자열, Prefix Array, LCP, 맨버-마이어스]
---

## 🔍 개요

![Suffix_Array-1](assets/img/posts/algorithms/Suffix_Array/Suffix_Array-1.png)

문자열 `S`에 대하여 `S[i:]`형태의 문자열들을 문자열 `S`의 **접미사(Suffix)**라고 부른다. 따라서, 문자열 `S`의 길이가 `N`이라고 한다면, 문자열 `S`는 서로다른 접미사 `N`개를 가지게 된다. 이러한 접미사들을 사전 순으로 정렬한 것을 **접미사 배열(Suffix Array)**이라고 부른다.

접미사 배열을 구하는 가장 쉬운 방법은, 모든 접미사들을 구한 뒤 정렬 알고리즘을 통해 정렬하는 방법이다. 이 방법은 각 문자열의 비교에 시간 복잡도 `O(N)`을 소요하므로, `O(NlogN)`의 시간복잡도를 가지는 정렬 알고리즘에 대해서 `O(N^2logN)`이 소요된다.

[![백준 로고](assets/img/posts/BOJ/boj-og.png)](https://www.acmicpc.net/problem/11656)

[백준 11656-접미사 배열](https://www.acmicpc.net/problem/11656) 문제가 단순 정렬로 해결할 수 있는 가장 대표적인 문제이다. 입력 문자열의 최대 길이가 1000이므로 직접 접미사들의 배열을 구성한 뒤 정렬 알고리즘으로 해결할 수 있다.

### 💻 코드
```c++
#include <bits/stdc++.h>
#define fastio cin.tie(0)->ios::sync_with_stdio(0)

using namespace std;
using ll = long long;
using pi = pair<int, int>;
using pll = pair<ll, ll>;
using vi = vector<int>;
using vvi = vector<vector<int>>;
using vvll = vector<vector<ll>>;
using vll = vector<ll>;
using vpi = vector<pi>;

int main() {
    fastio;

    string s;
    cin >> s;
    vector<string> v;
    for (int i = 0; i < s.size(); ++i) {
        v.push_back(s.substr(i));
    }
    sort(v.begin(), v.end());
    for (int i = 0; i < v.size(); ++i) {
        cout << v[i] << "\n";
    }
}
```

## 🔍 맨버-마이어스 알고리즘

하지만, 위와 같은 알고리즘은 입력 크기가 충분히 큰 [백준 9248-Suffix Array](https://www.acmicpc.net/problem/9248)과 같은 문제를 해결할 수 없다. 이 문제를 해결하기 위해 **<u>맨버-마이어스 알고리즘</u>**에 대해 알아본다.

**맨버-마이어스 알고리즘**은 `i`번째 단계에서 문자열의 `2^i`개의 문자를 비교하는 것으로 정렬을 수행한다. 문자열의 길이가 `N`이라고 한다면, 최대 단계는 `logN`이라는 점을 알 수 있다. 따라서, 각 정렬이 시간 복잡도 `O(NlogN)`을 가진다면 접미사 배열을 `O(Nlog^2N)` 내에 구할 수 있을 것이다.

하지만, `2^i`개의 문자를 비교할 때 단순하게 하나씩 비교한다면 단순 정렬과 차이가 없게 된다. 이를 해결하기 위해 접미사의 성질을 이용할 수 있다. 어떤 문자열의 접미사는 문자열에 속한 문자를 앞에서부터 지우면서 생성할 수 있다. 따라서, 문자열의 `S`의 접미사가 `S'`이라 한다면 `S'`의 접미사도 여전히 `S`의 접미사가 된다.

이 성질을 이용한다면 `2^i`개의 문자를 비교했을 때의 순서를 통해서 `2^(i + 1)`개의 문자를 비교했을 때의 순서를 `O(1)`에 알 수 있다.

![Suffix_Array-2](assets/img/posts/algorithms/Suffix_Array/Suffix_Array-2.png)

위 그림은 `i=1`인 경우의 결과를 통해 `i=2`인 경우의 순서를 알아내는 과정이다. 앞에서부터 `2^1=2`개의 문자만을 고려했을 때의 순서를 안다면 다음과 같은 과정을 통해서 `2^i=4`개의 문자를 고려했을 때의 순서를 알 수 있다.

1. 앞에서 `2^i`개를 고려했을 때의 순서를 확인한다. 만약 순서가 다르다면, 더 이상 확인할 필요가 없다.
2. 만약 순서가 같다면, 앞에서 `2^i`개를 제외한 문자열의 순서를 고려한다. 이 문자열들도 **접미사**이므로 앞에서 `2^i`개를 고려했을 때의 순서가 이미 알려져있다(이 두 문자열의 순서는 같을 수 없다. 이는 귀류법으로 쉽게 증명할 수 있다.).

### 💻 코드
```c++
#include <bits/stdc++.h>
#define fastio cin.tie(0)->ios::sync_with_stdio(0)

using namespace std;
using ll = long long;
using pi = pair<int, int>;
using pll = pair<ll, ll>;
using vi = vector<int>;
using vvi = vector<vector<int>>;
using vvll = vector<vector<ll>>
using vll = vector<ll>;
using vpi = vector<pi>;

constexpr int MAX = 500'000;

int sa[MAX], g[MAX];
string s;

int main() {
    fastio;

    cin >> s;

    for (int i = 0; i < s.size(); ++i) {
        sa[i] = i;
        g[i] = s[i];
    }

    for (int i = 0; (1 << i) < s.size(); i++) {
        auto cmp = [&] (int lhs, int rhs) {
            if (g[lhs] == g[rhs]) {
                lhs += 1 << i;
                rhs += 1 << i;
                if (lhs < s.size() && rhs < s.size()) {
                    return g[lhs] < g[rhs];
                } 
                return lhs > rhs;
            }
            return g[lhs] < g[rhs];
        };

        sort(begin(sa), begin(sa) + s.size(), cmp);

        int tmp[MAX] = { 0 };
        for (int j = 0; j < s.size() - 1; ++j) {
            tmp[j + 1] = tmp[j] + cmp(sa[j], sa[j + 1]);
        }

        for (int j = 0; j < s.size(); ++j) {
            g[sa[j]] = tmp[j];
        }

        if (tmp[s.size() - 1] == s.size() - 1) break;
    }
}
```

- `sa[i]`: 사전순으로 `i`번째 접미사. 접미사는 메모리 사용량을 줄이기 위해 시작지점의 인덱스로 표현한다.
- `g[i]`: `i`번째 인덱스에서 시작하는 접미사의 순서.

## 🔍 LCP(Longest Common Prefix)

접미사 배열을 구했다면, **LCP**를 손쉽게 구할 수 있다. LCP는 접미사들의 **가장 긴 공통 접두사**를 의미한다. **가장 긴 공통 접두사**는 접미사 배열 내에서 인접한 접미사 사이에서만 존재할 수 있으므로, 일반적으로 접미사 배열에서 인접한 접미사 사이의 공통 접두사의 길이의 배열을 구하는 방식으로 풀이한다(배열의 가장 큰 원소가 가장 긴 공통 접두사의 길이가 된다).

![Suffix_Array-3](assets/img/posts/algorithms/Suffix_Array/Suffix_Array-3.png)

위 그림에서 볼 수 있듯이 **LCP**또한 접두사의 성질을 이용한다면, 빠른 시간 내에 계산할 수 있다. 접미사 `S[i:]`에 대하여 공통 접두사의 길이가 `K`라면, 앞에서 문자 하나를 제거한 접미사 `S[i+1:]`의 공통 접두사 길이는 아무리 작아도 `K-1`보다 작을 수 없다. 이 성질을 통해서 `K-1`개 원소의 비교를 건너뛸 수 있다.

### 💻 코드

```c++
    int k = 0;
    for (int i = 0; i < s.size(); ++i) {
        if (g[i] == 0) continue;
        while (s[i + k] == s[sa[g[i] - 1] + k]) ++k;
        lcp[g[i]] = k--;
        k = max(k, 0);
    }
```

- `g[i]`: 접미사 배열을 완전히 계산하고 난 뒤, `g[i]`는 접미사 `S[i:]`의 사전순 순서를 의미한다. 이는 `sa[i]`에 대한 역함수로 생각할 수 있다. 따라서, `j: sa[g[i] - 1]`은 접미사 `S[i:]`보다 사전순으로 하나 앞서는 접미사 `S[j:]`를 의미한다.

## 📝 관련문제

[![백준 로고](assets/img/posts/BOJ/boj-og.png)](https://www.acmicpc.net/problem/9248)

[백준 9248-Suffix Array](https://www.acmicpc.net/problem/9248)

### 💻 코드

```c++
#include <bits/stdc++.h>
#define fastio cin.tie(0)->ios::sync_with_stdio(0)

using namespace std;
using ll = long long;
using pi = pair<int, int>;
using pll = pair<ll, ll>;
using vi = vector<int>;
using vvi = vector<vector<int>>;
using vvll = vector<vector<ll>>;
using vll = vector<ll>;
using vpi = vector<pi>;

constexpr int MAX = 500'000;

int sa[MAX], g[MAX], lcp[MAX];
string s;

int main() {
    fastio;

    cin >> s;

    for (int i = 0; i < s.size(); ++i) {
        sa[i] = i;
        g[i] = s[i];
    }

    for (int i = 0; (1 << i) < s.size(); i++) {
        auto cmp = [&] (int lhs, int rhs) {
            if (g[lhs] == g[rhs]) {
                lhs += 1 << i;
                rhs += 1 << i;
                if (lhs < s.size() && rhs < s.size()) {
                    return g[lhs] < g[rhs];
                } 
                return lhs > rhs;
            }
            return g[lhs] < g[rhs];
        };

        sort(begin(sa), begin(sa) + s.size(), cmp);

        int tmp[MAX] = { 0 };
        for (int j = 0; j < s.size() - 1; ++j) {
            tmp[j + 1] = tmp[j] + cmp(sa[j], sa[j + 1]);
        }

        for (int j = 0; j < s.size(); ++j) {
            g[sa[j]] = tmp[j];
        }

        if (tmp[s.size() - 1] == s.size() - 1) break;
    }

    int k = 0;
    for (int i = 0; i < s.size(); ++i) {
        if (g[i] == 0) continue;
        while (s[i + k] == s[sa[g[i] - 1] + k]) ++k;
        lcp[g[i]] = k--;
        k = max(k, 0);
    }

    for (int i = 0; i < s.size(); ++i) {
        cout << sa[i] + 1 << " ";
    }
    cout << "\n";
    for (int i = 0; i < s.size(); ++i) {
        if (i == 0) cout << 'x' << " ";
        else cout << lcp[i] << " ";
    }
}
```