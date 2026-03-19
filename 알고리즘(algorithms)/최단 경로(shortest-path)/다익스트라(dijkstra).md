# Dijkstra (다익스트라)

> 한 줄 정리: 가중치가 양수인 그래프에서 한 정점에서 모든 정점까지의 최단 거리를 구한다

## 1. 언제 쓰는가
- 가중치가 있는 그래프에서 최단 경로
- 가중치가 모두 양수일 때
- 단일 출발점 → 모든 정점 최단 거리

## 2. 핵심 아이디어
- 방문하지 않은 정점 중 거리가 가장 짧은 것부터 처리
- 힙으로 구현하면 O((V + E) log V)
- 음수 가중치 있으면 사용 불가 → 벨만포드 사용

## 3. 시간/공간 복잡도
| 구현 | 시간 |
|------|------|
| 힙 사용 | O((V + E) log V) |
| 힙 미사용 | O(V²) |

## 4. 기본 코드 (Python)

### 힙 기반 다익스트라
```python
import heapq
import sys

def dijkstra(graph, start, V):
    INF = sys.maxsize
    dist = [INF] * (V + 1)
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

# 입력 예시
import sys
from collections import defaultdict
input = sys.stdin.readline

V, E = map(int, input().split())
graph = defaultdict(list)
for _ in range(E):
    u, v, w = map(int, input().split())
    graph[u].append((v, w))  # 방향 그래프
```

### 경로 복원
```python
def dijkstra_path(graph, start, end, V):
    INF = sys.maxsize
    dist = [INF] * (V + 1)
    prev = [-1] * (V + 1)
    dist[start] = 0
    heap = [(0, start)]

    while heap:
        cost, node = heapq.heappop(heap)
        if cost > dist[node]:
            continue
        for neighbor, weight in graph[node]:
            new_cost = cost + weight
            if new_cost < dist[neighbor]:
                dist[neighbor] = new_cost
                prev[neighbor] = node
                heapq.heappush(heap, (new_cost, neighbor))

    # 경로 복원
    path = []
    cur = end
    while cur != -1:
        path.append(cur)
        cur = prev[cur]
    return dist[end], list(reversed(path))
```

## 5. 이걸 떠올려야 할 때
- "가중치 있는 최단 경로" + "음수 없음" → 다익스트라
- "특정 노드까지 최단 거리" → 다익스트라 후 dist[target]
- 가중치가 모두 1이면 → BFS로 충분

## 6. 자주 틀리는 포인트
- `if cost > dist[node]: continue` 빠뜨리면 시간초과
- 음수 가중치 있을 때 다익스트라 쓰면 오답
- 노드 번호가 1-indexed인지 확인 → dist 크기 V+1로
- 양방향 그래프면 양쪽 모두 추가

## 7. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 최단경로 | [백준 1753](https://www.acmicpc.net/problem/1753) | 다익스트라 기본 |
| 특정한 최단 경로 | [백준 1504](https://www.acmicpc.net/problem/1504) | 다익스트라 응용 |
| 배달 | [프로그래머스](https://school.programmers.co.kr/learn/courses/30/lessons/12978) | 다익스트라 |

## 9. 세 알고리즘 비교
| | 다익스트라 | 벨만포드 | 플로이드 워셜 |
|--|-----------|---------|-------------|
| 출발점 | 단일 | 단일 | 모든 쌍 |
| 음수 가중치 | 불가 | 가능 | 가능 |
| 음수 사이클 감지 | 불가 | 가능 | 가능 |
| 시간복잡도 | O((V+E) log V) | O(VE) | O(V³) |
| 적합한 V 크기 | 대규모 | 중간 | V ≤ 500 |
