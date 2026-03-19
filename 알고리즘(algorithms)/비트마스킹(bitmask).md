# Bitmask (비트마스킹)

> 한 줄 정리: 정수의 비트를 이용해 집합/상태를 표현하고 연산하는 방법

## 1. 언제 쓰는가
- 부분집합을 표현할 때
- 방문 상태를 정수 하나로 압축할 때
- DP with 비트마스크 (외판원 순회 등)
- 집합 연산을 빠르게 처리할 때

## 2. 핵심 아이디어
- N개의 원소 → N비트 정수로 상태 표현
- 각 비트가 해당 원소의 포함 여부를 나타냄
- 전체 부분집합 수: 2^N
- 비트 연산이 O(1)이라 상태 처리가 빠름

## 3. 비트 연산 정리
| 연산 | 코드 | 의미 |
|------|------|------|
| i번 비트 켜기 | `mask \| (1 << i)` | i번 원소 추가 |
| i번 비트 끄기 | `mask & ~(1 << i)` | i번 원소 제거 |
| i번 비트 확인 | `mask & (1 << i)` | i번 원소 포함 여부 |
| i번 비트 토글 | `mask ^ (1 << i)` | i번 원소 있으면 제거, 없으면 추가 |
| 전체 비트 켜기 | `(1 << N) - 1` | 모든 원소 포함 |
| 비트 수 세기 | `bin(mask).count('1')` | 포함된 원소 수 |

## 4. 기본 코드 (Python)

### 부분집합 순회
```python
N = 3
arr = ['a', 'b', 'c']

for mask in range(1 << N):  # 0 ~ 2^N - 1
    subset = []
    for i in range(N):
        if mask & (1 << i):
            subset.append(arr[i])
    print(subset)
```

### 비트 연산 예시
```python
mask = 0b0000  # 아무것도 없는 상태

mask |= (1 << 0)   # 0번 추가 → 0001
mask |= (1 << 2)   # 2번 추가 → 0101

# 0번 포함 여부
if mask & (1 << 0):
    print("0번 포함")

# 2번 제거
mask &= ~(1 << 2)  # → 0001

# 포함된 원소 수
print(bin(mask).count('1'))  # 1
```

## 5. 실전 패턴

### DP + 비트마스크 (외판원 순회 TSP)
```python
import sys

def tsp(graph, N):
    INF = sys.maxsize
    # dp[mask][i] = mask 상태에서 i번 도시에 있을 때 최소 비용
    dp = [[INF] * N for _ in range(1 << N)]
    dp[1][0] = 0  # 0번 도시에서 시작

    for mask in range(1 << N):
        for v in range(N):
            if dp[mask][v] == INF:
                continue
            if not (mask & (1 << v)):
                continue
            for next_v in range(N):
                if mask & (1 << next_v):  # 이미 방문
                    continue
                if graph[v][next_v] == 0:  # 간선 없음
                    continue
                new_mask = mask | (1 << next_v)
                dp[new_mask][next_v] = min(
                    dp[new_mask][next_v],
                    dp[mask][v] + graph[v][next_v]
                )

    full = (1 << N) - 1
    return min(dp[full][i] + graph[i][0] for i in range(N) if graph[i][0])
```

### 집합 연산
```python
A = 0b1010  # {1, 3}
B = 0b1100  # {2, 3}

A | B   # 합집합  → 0b1110 = {1, 2, 3}
A & B   # 교집합  → 0b1000 = {3}
A ^ B   # 대칭차  → 0b0110 = {1, 2}
A & ~B  # 차집합  → 0b0010 = {1}
```

## 6. 이걸 떠올려야 할 때
- "N ≤ 20인 부분집합 탐색" → 비트마스크
- "방문 상태를 정수로 표현" → 비트마스크 DP
- "외판원 순회(TSP)" → 비트마스크 DP 대표 문제
- "집합 연산을 빠르게" → 비트 연산

## 7. 자주 틀리는 포인트
- `1 << N`이 전체 경우의 수, 인덱스는 `0 ~ (1<<N) - 1`
- `~` 연산은 Python에서 부호 있는 정수라 `& ~(1<<i)` 시 의도치 않은 결과 가능 → `^ (1<<i)` 또는 마스크 AND 처리
- N이 크면 (N > 20) 메모리/시간 초과 → 비트마스크 DP는 N ≤ 20 이하에서만

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 외판원 순회 | [백준 2098](https://www.acmicpc.net/problem/2098) | 비트마스크 DP 대표 |
| 집합과 맵 | [백준 11723](https://www.acmicpc.net/problem/11723) | 비트마스크 집합 연산 |
| 부분집합의 합 | [백준 1182](https://www.acmicpc.net/problem/1182) | 비트마스크 부분집합 |
