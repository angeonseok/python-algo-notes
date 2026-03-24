# Fenwick Tree / BIT (펜윅 트리 / 이진 인덱스 트리)

> 한 줄 정리: 구간 합과 업데이트를 O(log N)에 처리하는 트리, 세그먼트 트리보다 구현이 간단하고 메모리가 적다

## 1. 언제 쓰는가
- 구간 합을 여러 번 조회하고 업데이트도 자주 할 때
- 세그먼트 트리보다 간단하게 구현하고 싶을 때
- 최솟값/최댓값은 필요 없고 합만 구할 때
- 역전 쌍(inversion count) 문제

## 2. 핵심 아이디어
- 비트 연산으로 각 인덱스가 담당하는 구간을 결정
- `i & (-i)`: i의 최하위 비트 → 담당 구간의 크기
- 1-indexed 배열로 구현 (0번 인덱스 사용 안 함)
- 업데이트: 담당 구간을 포함하는 상위 노드들 갱신
- 쿼리: prefix sum을 이용해 구간 합 계산

## 3. 시간/공간 복잡도
| 연산 | 복잡도 |
|------|--------|
| 업데이트 | O(log N) |
| 구간 합 쿼리 | O(log N) |
| 공간 | O(N) |

## 4. 기본 코드 (Python)

### 기본 구현
```python
class FenwickTree:
    def __init__(self, n):
        self.n = n
        self.tree = [0] * (n + 1)  # 1-indexed

    def update(self, i, delta):
        # i번 원소에 delta 더하기
        while i <= self.n:
            self.tree[i] += delta
            i += i & (-i)  # 최하위 비트만큼 올라감

    def prefix_sum(self, i):
        # 1~i 구간 합
        total = 0
        while i > 0:
            total += self.tree[i]
            i -= i & (-i)  # 최하위 비트 제거
        return total

    def query(self, left, right):
        # left~right 구간 합
        return self.prefix_sum(right) - self.prefix_sum(left - 1)

# 사용
ft = FenwickTree(5)
arr = [1, 2, 3, 4, 5]

# 초기화 (1-indexed)
for i, v in enumerate(arr, 1):
    ft.update(i, v)

ft.query(2, 4)    # 2+3+4 = 9
ft.update(3, 5)   # arr[3] += 5 → 3→8
ft.query(2, 4)    # 2+8+4 = 14
```

### i & (-i) 원리
```python
# i의 최하위 비트 = 담당하는 구간의 크기
# 예:
# i=1  → 0001 → 1 & (-1) = 1  → 1개 담당
# i=2  → 0010 → 2 & (-2) = 2  → 2개 담당
# i=4  → 0100 → 4 & (-4) = 4  → 4개 담당
# i=6  → 0110 → 6 & (-6) = 2  → 2개 담당

# update: i += i & (-i)  → 상위 노드로 이동
# query:  i -= i & (-i)  → 하위 구간으로 이동
```

### 역전 쌍 카운트 (inversion count)
```python
def count_inversions(arr):
    n = len(arr)
    # 값을 1~n으로 압축 (좌표 압축)
    sorted_arr = sorted(set(arr))
    rank = {v: i+1 for i, v in enumerate(sorted_arr)}

    ft = FenwickTree(n)
    inversions = 0

    for v in arr:
        r = rank[v]
        # 현재 값보다 큰 값 중 이미 처리된 것 = 역전 쌍
        inversions += ft.query(r + 1, n) if r + 1 <= n else 0
        ft.update(r, 1)

    return inversions
```

## 5. 세그먼트 트리 vs 펜윅 트리
| | 세그먼트 트리 | 펜윅 트리 |
|--|-------------|---------|
| 구현 난이도 | 보통 | 쉬움 |
| 구간 합 쿼리 | O(log N) | O(log N) |
| 업데이트 | O(log N) | O(log N) |
| 최솟값/최댓값 | 가능 | 불가 |
| 구간 업데이트 | 가능 (Lazy) | 제한적 |
| 메모리 | O(4N) | O(N) |
| 적합한 상황 | 최솟값/최댓값, 복잡한 쿼리 | 구간 합만 필요할 때 |

## 6. 이걸 떠올려야 할 때
- "구간 합 + 업데이트" → 펜윅 트리 (세그먼트 트리보다 간단)
- "역전 쌍 개수" → 펜윅 트리
- "최솟값/최댓값도 필요" → 세그먼트 트리로

## 7. 자주 틀리는 포인트
- 0-indexed 배열 사용 불가 → 반드시 1-indexed
- update는 `i += i & (-i)`, query는 `i -= i & (-i)` → 방향 헷갈리지 않기
- 구간 합은 prefix_sum(right) - prefix_sum(left-1) → left-1 빠뜨리는 경우
- 역전 쌍에서 좌표 압축 안 하면 트리 크기가 너무 커짐

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 구간 합 구하기 | [백준 2042](https://www.acmicpc.net/problem/2042) | 펜윅 트리 기본 |
| 역전 쌍 | [백준 1517](https://www.acmicpc.net/problem/1517) | 펜윅 트리 응용 |
| 좌표 압축 | [백준 18870](https://www.acmicpc.net/problem/18870) | 펜윅 트리 전처리 |
