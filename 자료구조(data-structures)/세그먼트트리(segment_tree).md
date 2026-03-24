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

### 구간 합 세그먼트 트리
```python
class SegmentTree:
    def __init__(self, arr):
        self.n = len(arr)
        self.tree = [0] * (4 * self.n)
        self.build(arr, 1, 0, self.n - 1)

    def build(self, arr, node, start, end):
        if start == end:
            self.tree[node] = arr[start]
        else:
            mid = (start + end) // 2
            self.build(arr, node*2,   start, mid)
            self.build(arr, node*2+1, mid+1, end)
            self.tree[node] = self.tree[node*2] + self.tree[node*2+1]

    def update(self, node, start, end, idx, val):
        if start == end:
            self.tree[node] = val
        else:
            mid = (start + end) // 2
            if idx <= mid:
                self.update(node*2,   start, mid,   idx, val)
            else:
                self.update(node*2+1, mid+1, end,   idx, val)
            self.tree[node] = self.tree[node*2] + self.tree[node*2+1]

    def query(self, node, start, end, left, right):
        if right < start or end < left:   # 범위 벗어남
            return 0
        if left <= start and end <= right:  # 완전히 포함
            return self.tree[node]
        mid = (start + end) // 2
        return (self.query(node*2,   start, mid,   left, right) +
                self.query(node*2+1, mid+1, end,   left, right))

# 사용
arr = [1, 2, 3, 4, 5]
st = SegmentTree(arr)

# arr[1:4] 구간 합 (0-indexed)
st.query(1, 0, st.n-1, 1, 3)   # 9

# arr[2] = 10으로 업데이트
st.update(1, 0, st.n-1, 2, 10)
```

### 최솟값 세그먼트 트리
```python
import sys

class MinSegmentTree:
    def __init__(self, arr):
        self.n = len(arr)
        self.tree = [sys.maxsize] * (4 * self.n)
        self.build(arr, 1, 0, self.n - 1)

    def build(self, arr, node, start, end):
        if start == end:
            self.tree[node] = arr[start]
        else:
            mid = (start + end) // 2
            self.build(arr, node*2,   start, mid)
            self.build(arr, node*2+1, mid+1, end)
            self.tree[node] = min(self.tree[node*2], self.tree[node*2+1])

    def update(self, node, start, end, idx, val):
        if start == end:
            self.tree[node] = val
        else:
            mid = (start + end) // 2
            if idx <= mid:
                self.update(node*2,   start, mid,   idx, val)
            else:
                self.update(node*2+1, mid+1, end,   idx, val)
            self.tree[node] = min(self.tree[node*2], self.tree[node*2+1])

    def query(self, node, start, end, left, right):
        if right < start or end < left:
            return sys.maxsize
        if left <= start and end <= right:
            return self.tree[node]
        mid = (start + end) // 2
        return min(self.query(node*2,   start, mid,   left, right),
                   self.query(node*2+1, mid+1, end,   left, right))
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
