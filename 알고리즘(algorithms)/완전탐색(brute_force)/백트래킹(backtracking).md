# Backtracking (백트래킹)

> 한 줄 정리: 재귀로 모든 경우를 탐색하되, 조건에 맞지 않으면 즉시 가지치기해서 탐색 범위를 줄인다

## 1. 언제 쓰는가
- 순열/조합/부분집합을 직접 구현할 때
- 조건을 만족하는 경우만 탐색해야 할 때
- N-Queen, 스도쿠 같은 제약 조건 탐색 문제
- 완전탐색인데 가지치기로 최적화가 가능할 때

## 2. 핵심 아이디어
- 재귀로 선택지를 하나씩 시도
- 현재 선택이 조건을 만족하지 않으면 즉시 되돌아감 (가지치기)
- 가지치기가 얼마나 효과적이냐에 따라 성능이 결정됨
- 완전탐색 + 가지치기 = 백트래킹

## 3. 시간복잡도
- 가지치기 없으면 완전탐색과 동일
- 순열: O(N!), 조합: O(2^N)
- 가지치기 효과에 따라 실제로는 훨씬 빠름

## 4. 기본 코드 (Python)

### 백트래킹 기본 구조
```python
def backtrack(상태):
    if 종료 조건:
        결과 저장
        return

    for 선택지 in 가능한_선택지:
        if 유망하지_않으면:   # 가지치기
            continue
        상태 변경              # 선택
        backtrack(다음 상태)
        상태 복원              # 되돌리기 (undo)
```

### 순열 직접 구현
```python
def permutation(arr, path, used):
    if len(path) == len(arr):
        print(path)
        return

    for i in range(len(arr)):
        if used[i]:
            continue
        used[i] = True
        path.append(arr[i])
        permutation(arr, path, used)
        path.pop()          # 되돌리기
        used[i] = False     # 되돌리기

arr = [1, 2, 3]
permutation(arr, [], [False] * len(arr))
```

### 조합 직접 구현
```python
def combination(arr, start, path, r):
    if len(path) == r:
        print(path)
        return

    for i in range(start, len(arr)):
        path.append(arr[i])
        combination(arr, i + 1, path, r)
        path.pop()          # 되돌리기

arr = [1, 2, 3, 4]
combination(arr, 0, [], 2)
```

### 부분집합 직접 구현
```python
def subset(arr, idx, path):
    print(path)             # 현재까지 선택된 원소

    for i in range(idx, len(arr)):
        path.append(arr[i])
        subset(arr, i + 1, path)
        path.pop()          # 되돌리기

arr = [1, 2, 3]
subset(arr, 0, [])
```

## 5. 실전 패턴

### N-Queen
```python
def n_queen(row, cols, diag1, diag2):
    if row == N:
        result.append(cols[:])
        return

    for col in range(N):
        if col in cols or (row - col) in diag1 or (row + col) in diag2:
            continue  # 가지치기
        cols.append(col)
        diag1.add(row - col)
        diag2.add(row + col)
        n_queen(row + 1, cols, diag1, diag2)
        cols.pop()
        diag1.remove(row - col)
        diag2.remove(row + col)
```

### 중복 없는 순열 (같은 값 있을 때)
```python
def permutation_unique(arr, path, used):
    if len(path) == len(arr):
        print(path)
        return

    seen = set()
    for i in range(len(arr)):
        if used[i] or arr[i] in seen:  # 같은 값 중복 방지
            continue
        seen.add(arr[i])
        used[i] = True
        path.append(arr[i])
        permutation_unique(arr, path, used)
        path.pop()
        used[i] = False
```

## 6. 이걸 떠올려야 할 때
- "조건을 만족하는 모든 경우" → 백트래킹
- itertools로 못 쓰는 상황 (조건이 복잡하거나 중간에 가지치기 필요) → 직접 구현
- "N-Queen", "스도쿠", "암호 만들기" 유형 → 백트래킹

## 7. 자주 틀리는 포인트
- 되돌리기(undo) 빠뜨리면 상태가 오염 → `path.pop()`, `used[i] = False` 짝 맞추기
- 가지치기 조건을 너무 늦게 체크하면 시간초과
- 중복 값이 있는 순열에서 `seen` 처리 안 하면 중복 결과 나옴
- `path`를 결과에 저장할 때 `result.append(path)` 하면 참조 저장 → `result.append(path[:])` 써야 함

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| N과 M (1) | [백준 15649](https://www.acmicpc.net/problem/15649) | 순열 백트래킹 기본 |
| N과 M (2) | [백준 15650](https://www.acmicpc.net/problem/15650) | 조합 백트래킹 기본 |
| N-Queen | [백준 9663](https://www.acmicpc.net/problem/9663) | 백트래킹 대표 문제 |
| 암호 만들기 | [백준 1759](https://www.acmicpc.net/problem/1759) | 조합 + 조건 가지치기 |
| 부분집합의 합 | [백준 1182](https://www.acmicpc.net/problem/1182) | 부분집합 백트래킹 |
