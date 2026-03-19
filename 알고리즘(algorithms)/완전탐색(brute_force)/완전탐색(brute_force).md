# Brute Force (완전 탐색)

> 한 줄 정리: 가능한 모든 경우를 다 시도해보는 방법, 느리지만 확실하다

## 1. 언제 쓰는가
- 경우의 수가 충분히 작을 때 (N ≤ 20 이하)
- 최적해를 반드시 구해야 할 때
- 다른 풀이가 떠오르지 않을 때 → 일단 완전탐색으로 구현 후 최적화
- itertools로 순열/조합을 바로 열거할 수 있을 때

## 2. 핵심 아이디어
- 모든 경우를 다 탐색 → 정답을 반드시 찾음
- 시간복잡도가 크기 때문에 N의 크기 먼저 확인
- 구현이 간단한 만큼 실수가 적음 → 시간 내에 통과되면 굳이 최적화 안 해도 됨

## 3. 시간복잡도
| 방법 | 복잡도 | 가능한 N |
|------|--------|---------|
| 순열 | O(N!) | N ≤ 8 |
| 조합/부분집합 | O(2^N) | N ≤ 20 |
| 이중 반복문 | O(N²) | N ≤ 5,000 |
| 삼중 반복문 | O(N³) | N ≤ 500 |

## 4. 기본 코드 (Python)

### 중첩 반복문
```python
# 모든 쌍 탐색 O(N²)
for i in range(n):
    for j in range(i + 1, n):
        # i, j 조합 처리
        pass
```

### 순열 (itertools)
```python
from itertools import permutations

arr = [1, 2, 3]

# 전체 순열 O(N!)
for p in permutations(arr):
    print(p)  # (1,2,3), (1,3,2), ...

# r개 선택 순열
for p in permutations(arr, 2):
    print(p)  # (1,2), (1,3), (2,1), ...
```

### 조합 (itertools)
```python
from itertools import combinations, combinations_with_replacement

arr = [1, 2, 3]

# r개 선택 조합
for c in combinations(arr, 2):
    print(c)  # (1,2), (1,3), (2,3)

# 중복 조합
for c in combinations_with_replacement(arr, 2):
    print(c)  # (1,1), (1,2), (1,3), (2,2), ...
```

### 부분집합 (비트마스크)
```python
arr = [1, 2, 3]
n = len(arr)

for mask in range(1 << n):  # 0 ~ 2^n - 1
    subset = []
    for i in range(n):
        if mask & (1 << i):
            subset.append(arr[i])
    print(subset)
```

## 5. 실전 패턴

### 최솟값/최댓값 완전탐색
```python
from itertools import permutations

result = float('inf')
for p in permutations(arr):
    result = min(result, calculate(p))
```

### 2차원 그리드 완전탐색
```python
for i in range(N):
    for j in range(M):
        if grid[i][j] == target:
            bfs(i, j)
```

### 모든 부분집합 합 탐색
```python
from itertools import combinations

arr = [1, 2, 3, 4]
target = 5

for r in range(1, len(arr) + 1):
    for c in combinations(arr, r):
        if sum(c) == target:
            print(c)
```

## 6. 이걸 떠올려야 할 때
- "N이 작다" → 완전탐색 가능
- "모든 경우", "최소/최대" → 완전탐색 후 최적화 고려
- 순열/조합을 직접 구현해야 한다면 → 백트래킹 파일 참고

## 7. 자주 틀리는 포인트
- N 크기 확인 안 하고 완전탐색 → 시간초과
- `permutations`, `combinations` 결과는 튜플 → 리스트 필요하면 `list(p)`
- 부분집합 비트마스크에서 `1 << n`이 전체 경우의 수 (0 포함)
- `combinations`는 인덱스 기준이라 중복 값 있어도 다른 원소로 취급

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 블랙잭 | [백준 2798](https://www.acmicpc.net/problem/2798) | 삼중 반복문 |
| 부분집합의 합 | [백준 1182](https://www.acmicpc.net/problem/1182) | 비트마스크 or 백트래킹 |
| 암호 만들기 | [백준 1759](https://www.acmicpc.net/problem/1759) | 조합 완전탐색 |
