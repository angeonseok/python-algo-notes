# Edmonds-Karp (에드몬즈-카프)

> 한 줄 정리: 포드-풀커슨에서 BFS로 증가 경로를 찾아 O(VE²)를 보장하는 알고리즘

## 1. 언제 쓰는가
- 일반적인 최대 유량 문제
- 포드-풀커슨보다 안정적인 시간복잡도가 필요할 때
- 디닉보다 구현이 간단할 때

## 2. 핵심 아이디어
- 포드-풀커슨과 동일하지만 DFS 대신 BFS로 최단 증가 경로 탐색
- BFS → 항상 최단 경로 선택 → O(VE²) 보장
- 역방향 간선으로 흐름 취소 가능

## 3. 시간복잡도
| | 복잡도 |
|--|--------|
| 시간 | O(VE²) |
| 포드-풀커슨 대비 | F에 무관하게 보장 |

## 4. 기본 코드 (Python)

```python
from collections import deque, defaultdict

def edmonds_karp(graph, capacity, source, sink, V):
    def bfs():
        visited = {source: None}
        queue = deque([source])
        while queue:
            v = queue.popleft()
            if v == sink:
                # 경로 복원
                path = []
                while v is not None:
                    path.append(v)
                    v = visited[v]
                return list(reversed(path))
            for u in graph[v]:
                if u not in visited and capacity[v][u] > 0:
                    visited[u] = v
                    queue.append(u)
        return None

    total = 0
    while True:
        path = bfs()
        if not path:
            break

        # 경로의 최소 잔여 용량
        flow = min(capacity[path[i]][path[i+1]] for i in range(len(path)-1))

        # 용량 갱신
        for i in range(len(path) - 1):
            capacity[path[i]][path[i+1]] -= flow
            capacity[path[i+1]][path[i]] += flow  # 역방향

        total += flow

    return total

# 입력 예시
V, E = map(int, input().split())
graph = defaultdict(list)
capacity = defaultdict(lambda: defaultdict(int))

for _ in range(E):
    u, v, c = map(int, input().split())
    if v not in graph[u]: graph[u].append(v)
    if u not in graph[v]: graph[v].append(u)
    capacity[u][v] += c
```

## 5. 포드-풀커슨 vs 에드몬즈-카프 vs 디닉
| | 포드-풀커슨 | 에드몬즈-카프 | 디닉 |
|--|-----------|-------------|------|
| 경로 탐색 | DFS | BFS | BFS + DFS |
| 시간복잡도 | O(EF) | O(VE²) | O(V²E) |
| 구현 난이도 | 쉬움 | 보통 | 어려움 |
| 실전 성능 | 느림 | 보통 | 빠름 |
| 권장 상황 | 개념 이해 | 일반적 | 대규모 |

## 6. 이걸 떠올려야 할 때
- 일반적인 최대 유량 문제 → 에드몬즈-카프
- 포드-풀커슨이 시간초과 → 에드몬즈-카프로 대체
- 그래프가 매우 크면 → 디닉 고려

## 7. 자주 틀리는 포인트
- 역방향 간선 추가 빠뜨리는 경우
- 중복 간선 처리 → capacity 합산, graph는 중복 없이
- BFS 경로 복원 시 역순 처리 빠뜨리는 경우

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 최대 유량 | [백준 6086](https://www.acmicpc.net/problem/6086) | 네트워크 플로우 기본 |
| 도시 왕복하기 2 | [백준 17412](https://www.acmicpc.net/problem/17412) | 에드몬즈-카프 |
