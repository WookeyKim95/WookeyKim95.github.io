---
layout: post
title:  "[백준] 1339번_단어 수학 C++"
subtitle:   
date: 2023-03-28 14:00:08 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1339번_단어 수학 C++<br/>
<br/>

수학적 규칙을 찾아서 풀 수 있었던 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>
#include <algorithm>

using namespace std;

bool cmp(int &a, int &b) {return a > b;}

int alpha[26];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        string _input;
        cin >> _input;

        int pow = 1;
        for (int j = _input.length()-1; j >= 0; --j)
        {
            alpha[_input[j]-'A'] += pow;
            pow *= 10;
        }
    }

    sort(alpha, alpha + 26, cmp);

    int ans = 0;
    int num = 9;
    for (int i = 0; i < 26; ++i)
    {
        if (alpha[i] == 0)
            break;
        ans += num * alpha[i];
        num--;
    }

    cout << ans;

    return 0;
}
```

아래에 있는 오답제출코드랑 아예 다른 로직이고 훨씬 쉬워보여서 조금 허망했다.<br/>

원리부터 설명하자면 아래와 같은 테스트 케이스가 있다고 해보자.

- ABCDE
- BACD
- CDF

이 테스트케이스를 다 더하면 값은 아래와 같아진다.

- (11000) * A + (2000) * B + (210) * C + (21) * D + E + F

즉, 각 알파벳에 곱하는 수가 가장 큰 순서대로 9부터 순서대로 배열하면 되는 것이다.<br/>

따라서 A = 9, B = 8, C = 7, D = 6, E = 5, F = 4 (or F = 5, E = 4)를 배치하면 가장 큰수가 나오게 된다.<br/>

그 과정을 구현한 코드가 위의 정답제출코드이다.<br/>

<br/>

## 오답제출코드<br/>
<br/>

정답과는 아예 다른 로직으로 짰었다.<br/>

그 내용을 아래에 서술한다.<br/>

```
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

bool Used[10] = {false};
int Number[26];
vector<pair<string, int>> Strings;

long long m_pow(int a, int b)
{
    if (b == 0)
        return 1;

    long long result = 1;
    for (int i = 0; i < b; ++i)
    {
        result *= a;
    }
    return result;
}

bool com(pair<string, int> a, pair<string, int> b)
{
    if (a.second > b.second)
        return true;
    else if (a.second == b.second)
        return a.first > b.first;
    else
        return false;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        string s;
        cin >> s;

        Strings.push_back({s, s.length()});
    }

    fill(Number, Number + 26, -1);
    sort(Strings.begin(), Strings.end(), com);

    int MaxLength = Strings[0].second;

    for (size_t i = 0; i < Strings.size(); ++i)
    {
        string newString;
        for (int j = MaxLength; j > Strings[i].second; --j)
        {
            newString += ' ';
        }
        newString += Strings[i].first;
        Strings[i].first = newString;
    }

    for (int j = 0; j < MaxLength; ++j)
    {
        for (size_t i = 0; i < Strings.size(); ++i)
        {
            if (Strings[i].first[j] != ' ')
            {
                for (int k = 9; k >= 0; --k)
                {
                    if (!Used[k] && Number[Strings[i].first[j] - 'A'] == -1)
                    {
                        Used[k] = true;
                        Number[Strings[i].first[j] - 'A'] = k;
                        break;
                    }
                }
            }
        }
    }

    long long ans = 0;

    for (size_t i = 0; i < Strings.size(); ++i)
    {
        for (int j = 0; j < MaxLength; ++j)
        {
            if (Strings[i].first[j] != ' ')
            {
                int power = MaxLength - j - 1;
                ans += (long long)Number[Strings[i].first[j] - 'A'] * m_pow(10, power);
            }
        }
    }

    cout << ans;

    return 0;
}
```

가장 높은 자리수에 있는 알파벳에서 부터 9, 8, 7, 6, ... 순으로 숫자를 부여하면 된다 생각했다.<br/>

그렇게 하면 가장 합이 가장 큰 경우로 출력 된다고 생각했다.<br/>

예를 들어, GCF, ACDEB 와 같은 경우가 있다고 하면

- A = 9, C = 8, D = 7, G = 6 (or G = 7, D = 6), E = 5, B = 4, F = 3 (or F = 4, B = 3)

으로 부여하면 99437로 가장 큰 합이 나온다.<br/>

다른 테스트 케이스로 ABC, DEF가 있다고 하면

- A = 9, D = 8 (or D = 9, A = 8)
- B = 7, E = 6 (or E = 7, B = 6)
- C = 5, F = 4 (or F = 5, C = 4)

로 부여하면 가장 큰 합이 나온다.<br/>

이렇게 나오도록 구현해주면 된다.<br/>

<br/>

개인적으로 굉장히 애를 먹었던 부분은 큰 자리수에 있는 수부터 수를 부여하는 과정이다.<br/>

우선, string과 string의 길이를 쌍으로 묶어서 vector에 저장하였다.<br/>

그리고 string의 길이가 긴 것 부터 앞으로 오도록 정렬을 실시하였다.<br/>

만약에 길이가 같다면 알파벳 순서가 앞서는 것이 먼저 오도록 정렬하였다.<br/>

```
bool com(pair<string, int> a, pair<string, int> b)
{
    if (a.second > b.second)
        return true;
    else if (a.second == b.second)
        return a.first > b.first;
    else
        return false;
}

sort(Strings.begin(), Strings.end(), com);
```

그리고 제일 앞에 있는 수부터 for문을 이용해서 숫자를 부여하려고 했다.<br/>

그렇다면 ACDEB, GCF와 같은 테스트케이스에서 가장 앞 자리에 있는 수부터 큰 수를 넣는 순서는 아래와 같다.

- A - C - D - G - E - B - F

하지만 이 과정은 이 상태로 그냥 for문만 돌려서는 방문 순서를 만들 수 없었다.<br/>

그래서 내가 사용한 방법은

- 가장 긴 문자열의 길이를 구하여 MaxLength로 정의한다.
- 나머지 문자열의 길이에서 MaxLength와 차이 만큼 앞에다가 빈칸을 넣는다.

즉, 오른쪽 정렬을 시키는 것이다.<br/>

이렇게 하면 ACDEB, GCF는 아래와 같이 정렬되며

```
ACDEB
  GCF
```

방문 순서는 아래와 같아진다.<br/>

- A - 빈칸 - C - 빈칸 - D - F - E - C(숫자를 넣는 과정은 생략) - B - F

이렇게 해서 빈칸인 경우나, 이미 숫자를 받은 경우는 생략을 하도록 구현하였다.<br/>

```
fill(Number, Number + 26, -1);
sort(Strings.begin(), Strings.end(), com);

int MaxLength = Strings[0].second;

for (size_t i = 0; i < Strings.size(); ++i)
{
    string newString;
    for (int j = MaxLength; j > Strings[i].second; --j)
    {
        newString += ' ';
    }
    newString += Strings[i].first;
    Strings[i].first = newString;
}

for (int j = 0; j < MaxLength; ++j)
{
    for (size_t i = 0; i < Strings.size(); ++i)
    {
        if (j >= MaxLength - Strings[i].second)
        {
            for (int k = 9; k >= 0; --k)
            {
                if (!Used[k] && Number[Strings[i].first[j] - 'A'] == -1)
                {
                    Used[k] = true;
                    Number[Strings[i].first[j] - 'A'] = k;
                    break;
                }
            }
        }
    }
}
```

그리고 부여받은 숫자 * 10^(자릿수)을 순차적으로 더해서 ans를 구하였다.<br/>

```
    long long ans = 0;

    for (size_t i = 0; i < Strings.size(); ++i)
    {
        for (int j = 0; j < MaxLength; ++j)
        {
            if (Strings[i].first[j] != ' ')
            {
                int power = MaxLength - j - 1;
                ans += (long long)Number[Strings[i].first[j] - 'A'] * m_pow(10, power);
            }
        }
    }

    cout << ans;
```

### 반례<br/>
<Br/>

```
5
ABCD
BCDE
CDEF
DEFG
EFGH
```

이 경우는 D나 E에다가 9를 부여해야한다. 왜냐하면 A는 1000의 자리에만 있으나,<br/>

D와 E는 네 자리에 모두 존재하여 부여받은 수 * 1111이 된다.<br/>

하지만 기존의 오답코드대로라면 A가 제일 먼저 있기 때문에 A가 9를 부여받게 되어<Br/>

오답이 출력된다.<br/>

난 여기서 코드를 엎어야 한다는 것을 깨달았다.<br/>