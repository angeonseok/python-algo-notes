# Prefix Sum (누적 합)

> 한 줄 정리: 구간 합을 O(1)에 구하기 위해 미리 합을 저장해두는 방법

## 1. 언제 쓰는가
- 구간 합을 여러 번 빠르게 구해야 할 때
- 2차원 격자의 부분 합
- 차이 배열(difference array)로 구간 업데이트

## 2. 핵심 아이디어
- prefix[i] = arr[0] + arr[1] + ... + arr[i-1]
- 구간 합 arr[l:r+1] = prefix[r+1] - prefix[l]
- 전처리 O(N), 쿼리 O(1)
- 2차원으로 확장 가능

## 3. 시간/공간 복잡도
| | 복잡도 |
|--|--------|
| 전처리 | O(N) |
| 구간 합 쿼리 | O(1) |
| 공간 | O(N) |

## 4. 기본 코드 (Python)

### 1차원 누적 합
```python
arr = [1, 2, 3, 4, 5]
n = len(arr)

prefix = [0] * (n + 1)
for i in range(n):
    prefix[i + 1] = prefix[i] + arr[i]

# arr[l:r+1] 구간 합 (0-indexed)
def range_sum(l, r):
    return prefix[r + 1] - prefix[l]

range_sum(1, 3)  # arr[1] + arr[2] + arr[3] = 9
```

### 2차원 누적 합
```python
N, M = len(grid), len(grid[0])
prefix = [[0] * (M + 1) for _ in range(N + 1)]

# 전처리
for i in range(1, N + 1):
    for j in range(1, M + 1):
        prefix[i][j] = (grid[i-1][j-1]
                      + prefix[i-1][j]
                      + prefix[i][j-1]
                      - prefix[i-1][j-1])

# (r1,c1) ~ (r2,c2) 구간 합 (0-indexed)
def range_sum_2d(r1, c1, r2, c2):
    return (prefix[r2+1][c2+1]
          - prefix[r1][c2+1]
          - prefix[r2+1][c1]
          + prefix[r1][c1])
```

### 차이 배열 (구간 업데이트 O(1))
```python
# arr[l:r+1]에 val을 더하는 연산을 여러 번 수행
n = 5
diff = [0] * (n + 1)

def range_update(l, r, val):
    diff[l] += val
    diff[r + 1] -= val

# 모든 업데이트 후 실제 배열 복원
range_update(1, 3, 5)
range_update(2, 4, 3)

result = [0] * n
result[0] = diff[0]
for i in range(1, n):
    result[i] = result[i-1] + diff[i]
```

## 5. 실전 패턴

### 나머지 합 (모듈러 누적 합)
```python
# 구간 합이 M으로 나누어 떨어지는 경우의 수
from collections import defaultdict

def count_divisible(arr, M):
    prefix = 0
    remainder_count = defaultdict(int)
    remainder_count[0] = 1
    result = 0

    for v in arr:
        prefix = (prefix + v) % M
        result += remainder_count[prefix]
        remainder_count[prefix] += 1

    return result
```

## 6. 이걸 떠올려야 할 때
- "구간 합을 여러 번 구해야 한다" → 누적 합
- "2차원 부분 합" → 2차원 누적 합
- "구간에 값을 더하는 연산이 많다" → 차이 배열
- "구간 합 % M" → 나머지 누적 합

## 7. 자주 틀리는 포인트
- prefix 배열 크기를 N+1로 해야 인덱스 에러 없음
- 2차원 누적 합 공식에서 포함/제외 범위 헷갈리는 경우 → 그림 그려서 확인
- 차이 배열에서 `diff[r+1]`이 범위 초과 → 크기를 n+1로 선언
- 0-indexed vs 1-indexed 혼용 주의

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 구간 합 구하기 4 | [백준 11659](https://www.acmicpc.net/problem/11659) | 1차원 누적 합 |
| 구간 합 구하기 5 | [백준 11660](https://www.acmicpc.net/problem/11660) | 2차원 누적 합 |
| 나머지 합 | [백준 10986](https://www.acmicpc.net/problem/10986) | 모듈러 누적 합 |
| imos법 | [백준 21315](https://www.acmicpc.net/problem/21315) | 차이 배열 |
