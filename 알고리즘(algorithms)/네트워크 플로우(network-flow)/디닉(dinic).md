# Dinic (디닉)

> 한 줄 정리: BFS로 레벨 그래프를 만들고 DFS로 막힌 흐름을 찾아 O(V²E)를 달성하는 가장 빠른 최대 유량 알고리즘

## 1. 언제 쓰는가
- 그래프가 크고 빠른 최대 유량이 필요할 때
- 에드몬즈-카프가 시간초과날 때
- 플래티넘 이상 네트워크 플로우 문제

## 2. 핵심 아이디어
- BFS로 레벨 그래프 구성 (소스에서 각 정점까지의 거리)
- DFS로 레벨 그래프 위에서 막힌 흐름(blocking flow) 탐색
- 이미 탐색한 간선은 건너뜀 (current edge 최적화)
- 레벨 그래프 재구성 반복

## 3. 시간복잡도
| | 복잡도 |
|--|--------|
| 시간 | O(V²E) |
| 단위 용량 그래프 | O(E√V) |
| 에드몬즈-카프 대비 | 실전에서 훨씬 빠름 |

## 4. 기본 코드 (Python)

```python
from collections import deque

class Dinic:
    def __init__(self, V):
        self.V = V
        self.graph = [[] for _ in range(V)]

    def add_edge(self, u, v, cap):
        # graph[u][i] = [to, cap, rev_idx]
        self.graph[u].append([v, cap, len(self.graph[v])])
        self.graph[v].append([u, 0,   len(self.graph[u]) - 1])  # 역방향

    def bfs(self, s, t):
        self.level = [-1] * self.V
        self.level[s] = 0
        queue = deque([s])
        while queue:
            v = queue.popleft()
            for to, cap, _ in self.graph[v]:
                if cap > 0 and self.level[to] == -1:
                    self.level[to] = self.level[v] + 1
                    queue.append(to)
        return self.level[t] != -1  # 싱크 도달 가능 여부

    def dfs(self, v, t, f):
        if v == t:
            return f
        while self.iter[v] < len(self.graph[v]):
            to, cap, rev = self.graph[v][self.iter[v]]
            if cap > 0 and self.level[v] < self.level[to]:
                d = self.dfs(to, t, min(f, cap))
                if d > 0:
                    self.graph[v][self.iter[v]][1] -= d
                    self.graph[to][rev][1] += d
                    return d
            self.iter[v] += 1
        return 0

    def max_flow(self, s, t):
        flow = 0
        while self.bfs(s, t):
            self.iter = [0] * self.V
            while True:
                f = self.dfs(s, t, float('inf'))
                if f == 0:
                    break
                flow += f
        return flow

# 사용 예시 (0-indexed)
V, E = map(int, input().split())
S, T = 0, V - 1  # 소스, 싱크

dinic = Dinic(V)
for _ in range(E):
    u, v, c = map(int, input().split())
    dinic.add_edge(u, v, c)

print(dinic.max_flow(S, T))
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
- 에드몬즈-카프가 시간초과 → 디닉
- 그래프가 매우 크거나 용량이 클 때
- 이분 매칭도 디닉으로 O(E√V)에 풀 수 있음

## 7. 자주 틀리는 포인트
- `iter` 배열 매 BFS마다 초기화 필수 → 안 하면 탐색 안 됨
- 역방향 간선 인덱스 관리 → `add_edge`에서 rev_idx 정확히 저장
- 레벨 그래프에서 `level[v] < level[to]` 조건 빠뜨리면 오답
- 0-indexed vs 1-indexed 혼용 주의

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 최대 유량 | [백준 6086](https://www.acmicpc.net/problem/6086) | 네트워크 플로우 기본 |
| 도시 왕복하기 2 | [백준 17412](https://www.acmicpc.net/problem/17412) | 디닉 |
| 중간 색 | [백준 1420](https://www.acmicpc.net/problem/1420) | 최소 컷 |
