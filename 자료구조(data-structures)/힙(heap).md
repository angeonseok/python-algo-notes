# Heap (힙)

> 한 줄 정리: 항상 최솟값(또는 최댓값)을 O(1)에 꺼낼 수 있는 구조

## 1. 언제 쓰는가
- 가장 작은/큰 값을 반복적으로 꺼내야 할 때
- K번째 최솟값/최댓값을 구할 때
- 우선순위가 있는 작업을 처리할 때 (우선순위 큐)
- 다익스트라 알고리즘
- 실시간으로 중앙값을 구해야 할 때

## 2. 핵심 아이디어
- Python `heapq`는 기본이 최소 힙
- 최댓값을 꺼내고 싶으면 값에 `-` 붙여서 넣기
- push → `heappush`
- pop → `heappop` (항상 가장 작은 값)
- 내부적으로 완전 이진 트리 구조

## 3. 시간복잡도
- push: O(log n)
- pop: O(log n)
- 최솟값 확인: O(1) → `heap[0]`
- heapify (리스트 → 힙): O(n)

## 4. 기본 코드 (Python)
```python
import heapq

heap = []

# push
heapq.heappush(heap, 3)
heapq.heappush(heap, 1)
heapq.heappush(heap, 2)

# pop (항상 최솟값)
val = heapq.heappop(heap)  # 1

# 최솟값 확인 (제거 안 함)
val = heap[0]

# 리스트를 힙으로 변환
arr = [3, 1, 2]
heapq.heapify(arr)

# 최대 힙 (음수로 저장)
heapq.heappush(heap, -3)
max_val = -heapq.heappop(heap)  # 3
```

## 5. 실전 패턴

### K번째 최솟값
```python
import heapq

def kth_smallest(arr, k):
    heapq.heapify(arr)
    for _ in range(k - 1):
        heapq.heappop(arr)
    return heapq.heappop(arr)
```

### 튜플로 우선순위 지정
```python
import heapq

heap = []
# (우선순위, 값) 형태로 넣으면 우선순위 기준으로 정렬
heapq.heappush(heap, (1, 'task_a'))
heapq.heappush(heap, (3, 'task_c'))
heapq.heappush(heap, (2, 'task_b'))

priority, task = heapq.heappop(heap)  # (1, 'task_a')
```

### 다익스트라
```python
import heapq

def dijkstra(graph, start):
    dist = {node: float('inf') for node in graph}
    dist[start] = 0
    heap = [(0, start)]  # (거리, 노드)

    while heap:
        cost, node = heapq.heappop(heap)
        if cost > dist[node]:  # 이미 처리된 노드 스킵
            continue
        for neighbor, weight in graph[node]:
            new_cost = cost + weight
            if new_cost < dist[neighbor]:
                dist[neighbor] = new_cost
                heapq.heappush(heap, (new_cost, neighbor))

    return dist
```

### 중앙값 유지 (최대 힙 + 최소 힙)
```python
import heapq

# 왼쪽 절반: 최대 힙 (음수 저장), 오른쪽 절반: 최소 힙
left, right = [], []

def add(num):
    heapq.heappush(left, -num)
    heapq.heappush(right, -heapq.heappop(left))
    if len(left) < len(right):
        heapq.heappush(left, -heapq.heappop(right))

def get_median():
    return -left[0]
```

## 6. 이걸 떠올려야 할 때
- "매번 최솟값/최댓값을 꺼내야 한다" → 힙
- "K개만 유지하면서 최솟값/최댓값" → 힙
- "최단 경로" → 다익스트라 + 힙
- "우선순위가 있다" → 힙
- 정렬하면 O(n log n)인데 매번 꺼내는 게 반복된다면 → 힙이 더 적합

## 7. 자주 틀리는 포인트
- Python heapq는 최소 힙만 지원 → 최대 힙은 반드시 `-` 붙여서 넣고 꺼낼 때도 `-` 붙여야 함
- 다익스트라에서 `if cost > dist[node]: continue` 빠뜨리면 시간초과
- `heap[0]`으로 최솟값 확인은 가능하지만 중간 인덱스 접근은 의미 없음 (정렬된 배열 아님)
- heapify는 O(n)이지만 매번 push하면 O(n log n) → 한꺼번에 만들 땐 heapify가 유리

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 최소 힙 | [백준 1927](https://www.acmicpc.net/problem/1927) | 힙 기본 |
| 최대 힙 | [백준 11279](https://www.acmicpc.net/problem/11279) | 최대 힙 (-) 처리 |
| 다익스트라 | [백준 1753](https://www.acmicpc.net/problem/1753) | 힙 + 다익스트라 |
| 절댓값 힙 | [백준 11286](https://www.acmicpc.net/problem/11286) | 튜플로 우선순위 |
| 중앙값 구하기 | [백준 1655](https://www.acmicpc.net/problem/1655) | 최대 힙 + 최소 힙 |
