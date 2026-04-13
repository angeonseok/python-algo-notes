# Segment Tree (세그먼트 트리)

> 한 줄 정리: 구간 합/최솟값/최댓값을 O(log N)에 조회하고 업데이트하는 트리 자료구조

## 1. 언제 쓰는가
- 구간 합/최솟값/최댓값을 여러 번 조회할 때
- 값이 동적으로 업데이트되는 상황
- 누적 합은 업데이트가 O(N)이라 느릴 때 → 세그먼트 트리로 대체

## 2. 핵심 아이디어
- 배열을 트리 형태로 저장, 각 노드가 구간의 합/최솟값을 저장
- 리프 노드: 원소 하나
- 내부 노드: 자식 노드들의 합/최솟값
- 1-indexed 배열로 구현, 부모 i → 자식 2i, 2i+1

## 3. 시간/공간 복잡도
| 연산 | 복잡도 |
|------|--------|
| 빌드 | O(N) |
| 업데이트 | O(log N) |
| 구간 쿼리 | O(log N) |
| 공간 | O(4N) |

## 4. 기본 코드 (Python)

### 함수형 (전역 배열, 코테 권장)
```python
import sys
sys.setrecursionlimit(100000)

N = 0
tree = []

def build(arr, node, start, end):
    if start == end:
        tree[node] = arr[start]
    else:
        mid = (start + end) // 2
        build(arr, node*2,   start, mid)
        build(arr, node*2+1, mid+1, end)
        tree[node] = tree[node*2] + tree[node*2+1]

def update(node, start, end, idx, val):
    if start == end:
        tree[node] = val
    else:
        mid = (start + end) // 2
        if idx <= mid: update(node*2,   start, mid,   idx, val)
        else:          update(node*2+1, mid+1, end,   idx, val)
        tree[node] = tree[node*2] + tree[node*2+1]

def query(node, start, end, left, right):
    if right < start or end < left: return 0
    if left <= start and end <= right: return tree[node]
    mid = (start + end) // 2
    return (query(node*2,   start, mid,   left, right) +
            query(node*2+1, mid+1, end,   left, right))

# 사용
arr = [1, 2, 3, 4, 5]
N = len(arr)
tree = [0] * (4 * N)
build(arr, 1, 0, N-1)

query(1, 0, N-1, 1, 3)   # arr[1:4] 구간 합 = 9
update(1, 0, N-1, 2, 10)  # arr[2] = 10
query(1, 0, N-1, 1, 3)   # 16
```

### 클래스형 (호출 단순화, 트리 2개 이상 쓸 때 유용)
```python
import sys

class SegmentTree:
    def __init__(self, arr, func=sum, default=0):
        self.n       = len(arr)
        self.func    = func      # sum, min, max 등 연산 함수
        self.default = default   # 범위 벗어날 때 반환값
        self.tree    = [default] * (4 * self.n)
        self._build(arr, 1, 0, self.n - 1)

    def _build(self, arr, node, start, end):
        if start == end:
            self.tree[node] = arr[start]
        else:
            mid = (start + end) // 2
            self._build(arr, node*2,   start, mid)
            self._build(arr, node*2+1, mid+1, end)
            self.tree[node] = self.func([self.tree[node*2], self.tree[node*2+1]])

    def _update(self, node, start, end, idx, val):
        if start == end:
            self.tree[node] = val
        else:
            mid = (start + end) // 2
            if idx <= mid: self._update(node*2,   start, mid,   idx, val)
            else:          self._update(node*2+1, mid+1, end,   idx, val)
            self.tree[node] = self.func([self.tree[node*2], self.tree[node*2+1]])

    def _query(self, node, start, end, left, right):
        if right < start or end < left: return self.default
        if left <= start and end <= right: return self.tree[node]
        mid = (start + end) // 2
        return self.func([self._query(node*2,   start, mid,   left, right),
                          self._query(node*2+1, mid+1, end,   left, right)])

    # 외부 호출은 이것만 사용 (node, start, end 안 넘겨도 됨)
    def update(self, idx, val):
        self._update(1, 0, self.n - 1, idx, val)

    def query(self, left, right):
        return self._query(1, 0, self.n - 1, left, right)

# 사용
arr = [1, 2, 3, 4, 5]

st_sum = SegmentTree(arr, func=sum, default=0)            # 구간 합
st_min = SegmentTree(arr, func=min, default=sys.maxsize)  # 구간 최솟값
st_max = SegmentTree(arr, func=max, default=0)            # 구간 최댓값

st_sum.query(1, 3)   # 9
st_min.query(1, 3)   # 2
st_max.query(1, 3)   # 4

st_sum.update(2, 10)  # arr[2] = 10
st_sum.query(1, 3)    # 16
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
- "구간 합 + 업데이트가 많다" → 세그먼트 트리 or 펜윅 트리
- "구간 최솟값/최댓값" → 세그먼트 트리 (펜윅은 불가)
- 누적 합은 업데이트 O(N) → 업데이트 많으면 세그먼트 트리

## 7. 자주 틀리는 포인트
- 트리 크기를 4*N으로 잡아야 안전 (2*N이면 부족할 수 있음)
- query에서 범위 벗어남 조건 (`right < start or end < left`) 순서 헷갈리는 경우
- update 후 부모 노드 갱신 빠뜨리는 경우
- 0-indexed vs 1-indexed 혼용 주의

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 구간 합 구하기 | [백준 2042](https://www.acmicpc.net/problem/2042) | 세그먼트 트리 기본 |
| 최솟값 | [백준 10868](https://www.acmicpc.net/problem/10868) | 최솟값 세그먼트 트리 |
| 수열과 쿼리 | [백준 13537](https://www.acmicpc.net/problem/13537) | 세그먼트 트리 응용 |
