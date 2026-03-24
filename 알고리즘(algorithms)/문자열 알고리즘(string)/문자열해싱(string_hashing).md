# String Hashing (문자열 해싱)

> 한 줄 정리: 문자열을 숫자로 변환해 부분 문자열 비교를 O(1)에 처리한다

## 1. 언제 쓰는가
- 두 부분 문자열이 같은지 O(1)에 비교
- 긴 문자열에서 패턴 탐색 (라빈-카프)
- 팰린드롬 판별
- 문자열 집합에서 중복 탐색

## 2. 핵심 아이디어
- 문자열 → 해시값(정수)으로 변환
- 같은 문자열 → 같은 해시값
- 누적 해시로 부분 문자열 해시를 O(1)에 계산
- 충돌 방지를 위해 큰 소수(MOD)와 BASE 사용

## 3. 시간/공간 복잡도
| | 복잡도 |
|--|--------|
| 전처리 | O(N) |
| 부분 문자열 해시 | O(1) |
| 공간 | O(N) |

## 4. 기본 코드 (Python)

### 단일 해시
```python
MOD  = 1_000_000_007
BASE = 31  # 소문자면 31, 대소문자 포함이면 131

def build_hash(s):
    n = len(s)
    h = [0] * (n + 1)
    p = [1] * (n + 1)  # BASE의 거듭제곱

    for i in range(n):
        h[i+1] = (h[i] * BASE + ord(s[i])) % MOD
        p[i+1] = p[i] * BASE % MOD

    return h, p

def get_hash(h, p, l, r):
    # s[l:r+1]의 해시값 (0-indexed)
    return (h[r+1] - h[l] * p[r-l+1]) % MOD
```

### 이중 해시 (충돌 방지)
```python
MOD1, BASE1 = 1_000_000_007, 31
MOD2, BASE2 = 998_244_353,   37

def build_double_hash(s):
    n = len(s)
    h1 = [0] * (n + 1); p1 = [1] * (n + 1)
    h2 = [0] * (n + 1); p2 = [1] * (n + 1)

    for i in range(n):
        h1[i+1] = (h1[i] * BASE1 + ord(s[i])) % MOD1
        p1[i+1] = p1[i] * BASE1 % MOD1
        h2[i+1] = (h2[i] * BASE2 + ord(s[i])) % MOD2
        p2[i+1] = p2[i] * BASE2 % MOD2

    return h1, p1, h2, p2

def get_double_hash(h1, p1, h2, p2, l, r):
    hash1 = (h1[r+1] - h1[l] * p1[r-l+1]) % MOD1
    hash2 = (h2[r+1] - h2[l] * p2[r-l+1]) % MOD2
    return (hash1, hash2)
```

### 라빈-카프 (패턴 탐색)
```python
def rabin_karp(text, pattern):
    N, M = len(text), len(pattern)
    ht, pt = build_hash(text), build_hash(pattern)
    h_text, p_text = ht
    h_pat,  p_pat  = pt

    target = get_hash(h_pat, p_pat, 0, M - 1)
    result = []

    for i in range(N - M + 1):
        if get_hash(h_text, p_text, i, i + M - 1) == target:
            result.append(i)

    return result
```

### 팰린드롬 판별
```python
def is_palindrome(s, l, r, h, p, hr, pr):
    # h, p: 정방향 해시
    # hr, pr: 역방향 해시
    forward  = get_hash(h,  p,  l, r)
    backward = get_hash(hr, pr, len(s)-1-r, len(s)-1-l)
    return forward == backward

# 역방향 해시 빌드
def build_reverse_hash(s):
    return build_hash(s[::-1])
```

## 5. 이걸 떠올려야 할 때
- "부분 문자열 비교를 O(1)에" → 문자열 해싱
- "긴 문자열에서 패턴 탐색" → 라빈-카프 (KMP 대안)
- "팰린드롬 판별" → 정방향 + 역방향 해시 비교
- 충돌이 걱정되면 → 이중 해시 사용

## 6. 자주 틀리는 포인트
- 해시값이 음수 나올 수 있음 → `% MOD` 후 `+ MOD) % MOD` 처리
- 단일 해시는 충돌 가능 → 중요한 문제엔 이중 해시
- BASE를 너무 작게 잡으면 충돌 증가
- `p` 배열 크기를 N+1로 잡아야 인덱스 오류 없음

## 7. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 문자열 매칭 | [백준 16916](https://www.acmicpc.net/problem/16916) | 해싱 기본 |
| 팰린드롬 | [백준 10942](https://www.acmicpc.net/problem/10942) | 해싱 + 팰린드롬 |
| 부분 문자열 | [백준 16900](https://www.acmicpc.net/problem/16900) | 해싱 응용 |
