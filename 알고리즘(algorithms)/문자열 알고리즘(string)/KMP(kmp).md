# KMP (Knuth-Morris-Pratt)

> 한 줄 정리: 문자열에서 패턴을 O(N+M)에 찾는 알고리즘, 실패 함수로 불필요한 비교를 건너뛴다

## 1. 언제 쓰는가
- 긴 문자열에서 특정 패턴이 등장하는 위치를 모두 찾을 때
- 단순 탐색 O(NM)이 시간초과날 때
- 패턴 매칭이 반복적으로 필요할 때

## 2. 핵심 아이디어
- 실패 함수(failure function): 패턴의 접두사이면서 접미사인 최장 길이
- 불일치 발생 시 처음부터 다시 비교하지 않고 실패 함수로 이동
- 전처리 O(M) + 탐색 O(N) = O(N+M)

## 3. 시간/공간 복잡도
| | 복잡도 |
|--|--------|
| 전처리 (실패 함수) | O(M) |
| 탐색 | O(N) |
| 전체 | O(N + M) |

## 4. 기본 코드 (Python)

### 실패 함수 (failure function)
```python
def get_failure(pattern):
    M = len(pattern)
    failure = [0] * M
    j = 0

    for i in range(1, M):
        while j > 0 and pattern[i] != pattern[j]:
            j = failure[j - 1]  # 이전 실패 함수로 이동
        if pattern[i] == pattern[j]:
            j += 1
            failure[i] = j

    return failure
```

### KMP 탐색
```python
def kmp(text, pattern):
    N, M = len(text), len(pattern)
    failure = get_failure(pattern)
    result = []
    j = 0

    for i in range(N):
        while j > 0 and text[i] != pattern[j]:
            j = failure[j - 1]  # 실패 함수로 이동
        if text[i] == pattern[j]:
            j += 1
        if j == M:  # 패턴 완전 매칭
            result.append(i - M + 1)  # 시작 인덱스
            j = failure[j - 1]        # 다음 탐색 위치

    return result

# 사용
text    = "aabaabaab"
pattern = "aab"
print(kmp(text, pattern))  # [0, 3, 6]
```

## 5. 실전 패턴

### 패턴 등장 횟수만 필요할 때
```python
def kmp_count(text, pattern):
    failure = get_failure(pattern)
    count = 0
    j = 0
    for c in text:
        while j > 0 and c != pattern[j]:
            j = failure[j - 1]
        if c == pattern[j]:
            j += 1
        if j == len(pattern):
            count += 1
            j = failure[j - 1]
    return count
```

### 문자열 주기 찾기
```python
def string_period(s):
    failure = get_failure(s)
    n = len(s)
    period = n - failure[n - 1]
    if n % period == 0:
        return period  # s는 길이 period인 문자열의 반복
    return n           # 주기 없음
```

## 6. 이걸 떠올려야 할 때
- "문자열에서 패턴 찾기" + N, M이 크면 → KMP
- "문자열의 주기" → 실패 함수 활용
- 단순 `in` 연산자는 최악 O(NM) → 큰 입력엔 KMP

## 7. 자주 틀리는 포인트
- 실패 함수에서 `i`는 1부터 시작 (`failure[0]`은 항상 0)
- 매칭 후 `j = failure[j-1]`로 이동해야 다음 탐색 가능 (빠뜨리면 중복 매칭 누락)
- 결과 인덱스가 0-indexed인지 1-indexed인지 문제에 따라 조정

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 찾기 | [백준 1786](https://www.acmicpc.net/problem/1786) | KMP 기본 |
| 문자열 주기 | [백준 1305](https://www.acmicpc.net/problem/1305) | 실패 함수 응용 |
| 광고 | [백준 1701](https://www.acmicpc.net/problem/1701) | 실패 함수 심화 |
