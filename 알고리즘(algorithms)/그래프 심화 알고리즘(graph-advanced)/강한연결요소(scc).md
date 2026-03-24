# SCC (Strongly Connected Components, 강한 연결 요소)

> 한 줄 정리: 방향 그래프에서 서로 왕복 가능한 정점들의 묶음을 찾는 알고리즘

## 1. 언제 쓰는가
- 방향 그래프에서 순환 구조를 찾을 때
- 2-SAT 문제
- 그래프를 DAG로 압축할 때
- 사이클이 있는 방향 그래프 분석

## 2. 핵심 아이디어
- SCC: 집합 내 임의의 두 정점 u, v에 대해 u→v, v→u 경로가 모두 존재
- 같은 SCC 내 정점들은 하나의 노드로 압축 가능 → DAG로 변환
- 구현 방법: 코사라주(DFS 2번), 타잔(DFS 1번)

## 3. 시간복잡도
| 방법 | 복잡도 |
|------|--------|
| 코사라주 | O(V + E) |
| 타잔 | O(V + E) |

## 4. 기본 코드 (Python)

### 코사라주 알고리즘 (구현 쉬움, 코테 권장)
```python
import sys
from collections import defaultdict
sys.setrecursionlimit(100000)

def kosaraju(graph, reverse_graph, V):
    visited = [False] * (V + 1)
    order = []  # 종료 순서

    # 1단계: 원래 그래프에서 DFS, 종료 순서 기록
    def dfs1(v):
        visited[v] = True
        for neighbor in graph[v]:
            if not visited[neighbor]:
                dfs1(neighbor)
        order.append(v)

    for v in range(1, V + 1):
        if not visited[v]:
            dfs1(v)

    # 2단계: 역방향 그래프에서 종료 순서 역순으로 DFS
    visited = [False] * (V + 1)
    sccs = []

    def dfs2(v, scc):
        visited[v] = True
        scc.append(v)
        for neighbor in reverse_graph[v]:
            if not visited[neighbor]:
                dfs2(neighbor, scc)

    while order:
        v = order.pop()
        if not visited[v]:
            scc = []
            dfs2(v, scc)
            sccs.append(scc)

    return sccs

# 입력 예시
V, E = map(int, input().split())
graph = defaultdict(list)
reverse_graph = defaultdict(list)

for _ in range(E):
    u, v = map(int, input().split())
    graph[u].append(v)
    reverse_graph[v].append(u)  # 역방향 그래프

sccs = kosaraju(graph, reverse_graph, V)
print(len(sccs))  # SCC 개수
```

### 타잔 알고리즘 (DFS 1번, 구현 까다로움)
```python
import sys
sys.setrecursionlimit(100000)

def tarjan(graph, V):
    order = [0]           # 방문 순서 (리스트로 전역 관리)
    visited = [0] * (V + 1)   # 방문 순서 기록
    low = [0] * (V + 1)       # 도달 가능한 최소 순서
    on_stack = [False] * (V + 1)
    stack = []
    sccs = []

    def dfs(v):
        order[0] += 1
        visited[v] = low[v] = order[0]
        stack.append(v)
        on_stack[v] = True

        for neighbor in graph[v]:
            if not visited[neighbor]:
                dfs(neighbor)
                low[v] = min(low[v], low[neighbor])
            elif on_stack[neighbor]:
                low[v] = min(low[v], visited[neighbor])

        # v가 SCC의 루트이면
        if low[v] == visited[v]:
            scc = []
            while True:
                u = stack.pop()
                on_stack[u] = False
                scc.append(u)
                if u == v:
                    break
            sccs.append(scc)

    for v in range(1, V + 1):
        if not visited[v]:
            dfs(v)

    return sccs
```

## 5. 코사라주 vs 타잔
| | 코사라주 | 타잔 |
|--|---------|------|
| DFS 횟수 | 2번 | 1번 |
| 역방향 그래프 | 필요 | 불필요 |
| 구현 난이도 | 쉬움 | 어려움 |
| 코테 권장 | ✓ | |

## 6. SCC 압축 → DAG 변환
```python
# 각 정점이 속한 SCC 번호 부여
scc_id = [0] * (V + 1)
for i, scc in enumerate(sccs):
    for v in scc:
        scc_id[v] = i

# SCC 간 간선으로 DAG 구성
dag = defaultdict(set)
for v in range(1, V + 1):
    for neighbor in graph[v]:
        if scc_id[v] != scc_id[neighbor]:  # 다른 SCC 간 간선만
            dag[scc_id[v]].add(scc_id[neighbor])
```

## 7. 이걸 떠올려야 할 때
- "방향 그래프에서 순환 구조" → SCC
- "2-SAT" → SCC 필수
- "그래프를 단순화해서 위상 정렬" → SCC로 DAG 압축 후 위상 정렬

## 8. 자주 틀리는 포인트
- 코사라주에서 역방향 그래프 빠뜨리는 경우
- 타잔에서 `on_stack` 체크 안 하면 이미 완성된 SCC를 참조
- 재귀 깊이 → `sys.setrecursionlimit` 필수
- SCC 압축 후 같은 SCC 내 간선은 제거해야 함

## 9. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 강한 연결 요소 | [백준 2150](https://www.acmicpc.net/problem/2150) | SCC 기본 |
| 도미노 | [백준 4196](https://www.acmicpc.net/problem/4196) | SCC + DAG |
| 2-SAT | [백준 11280](https://www.acmicpc.net/problem/11280) | SCC 응용 |
