# DFS / BFS (깊이 우선 탐색 / 너비 우선 탐색)

> 한 줄 정리: 그래프/트리를 탐색하는 두 가지 방법, DFS는 깊게 파고들고 BFS는 가까운 것부터 퍼져나간다

## 1. 언제 쓰는가

### DFS
- 경로가 존재하는지 확인
- 모든 경우를 탐색해야 할 때 (백트래킹)
- 연결 요소 개수, 사이클 감지
- 트리 순회

### BFS
- 최단 거리/최소 횟수
- 레벨별로 처리해야 할 때
- 가중치 없는 그래프에서 최단 경로
- 멀티 소스 탐색 (시작점이 여러 개)

## 2. 핵심 차이

| | DFS | BFS |
|--|-----|-----|
| 자료구조 | 스택 (재귀) | 큐 |
| 탐색 방식 | 깊이 우선 | 너비 우선 |
| 최단 거리 | 보장 안 됨 | 보장됨 (가중치 없을 때) |
| 메모리 | O(깊이) | O(너비) |
| 적합한 문제 | 경로 탐색, 백트래킹 | 최단 거리, 레벨 탐색 |

## 3. 시간/공간 복잡도
| | 시간 | 공간 |
|--|------|------|
| DFS | O(V + E) | O(V) |
| BFS | O(V + E) | O(V) |

## 4. 기본 코드 (Python)

### DFS - 재귀
```python
def dfs(graph, node, visited):
    visited.add(node)
    print(node)

    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited)

visited = set()
dfs(graph, start, visited)
```

### DFS - 스택 (반복문)
```python
def dfs(graph, start):
    stack = [start]
    visited = set([start])

    while stack:
        node = stack.pop()
        print(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                stack.append(neighbor)
```

### BFS
```python
from collections import deque

def bfs(graph, start):
    queue = deque([start])
    visited = set([start])

    while queue:
        node = queue.popleft()
        print(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

### BFS 최단 거리
```python
from collections import deque

def bfs(graph, start):
    queue = deque([(start, 0)])  # (노드, 거리)
    visited = set([start])

    while queue:
        node, dist = queue.popleft()
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, dist + 1))
```

### 2차원 그리드 BFS
```python
from collections import deque

dx = [0, 0, 1, -1]
dy = [1, -1, 0, 0]

def bfs(grid, sx, sy):
    N, M = len(grid), len(grid[0])
    queue = deque([(sx, sy, 0)])  # (x, y, 거리)
    visited = [[False] * M for _ in range(N)]
    visited[sx][sy] = True

    while queue:
        x, y, dist = queue.popleft()
        for i in range(4):
            nx, ny = x + dx[i], y + dy[i]
            if 0 <= nx < N and 0 <= ny < M and not visited[nx][ny]:
                visited[nx][ny] = True
                queue.append((nx, ny, dist + 1))
```

### 멀티 소스 BFS (시작점 여러 개)
```python
from collections import deque

def multi_bfs(grid, sources):
    queue = deque()
    visited = set()

    # 모든 시작점을 큐에 넣고 시작
    for src in sources:
        queue.append((src, 0))
        visited.add(src)

    while queue:
        node, dist = queue.popleft()
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, dist + 1))
```

## 5. 이걸 떠올려야 할 때
- "최단 거리/최소 횟수" → BFS
- "모든 경로 탐색", "경우의 수" → DFS
- "연결 요소 개수" → DFS or BFS 둘 다
- "동시에 여러 곳에서 퍼져나감" → 멀티 소스 BFS
- "레벨별로 처리" → BFS

## 6. 자주 틀리는 포인트
- visited 체크를 dequeue할 때 하면 같은 노드가 큐에 중복으로 쌓임 → enqueue할 때 바로 체크
- DFS 재귀에서 `sys.setrecursionlimit` 안 하면 깊은 그래프에서 RecursionError
- 2차원 BFS에서 범위 체크 순서 틀리면 IndexError → `0 <= nx < N and 0 <= ny < M` 먼저
- DFS 스택 방식은 재귀 방식과 탐색 순서가 다를 수 있음

## 7. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| DFS와 BFS | [백준 1260](https://www.acmicpc.net/problem/1260) | DFS/BFS 기본 |
| 미로 탈출 | [백준 2178](https://www.acmicpc.net/problem/2178) | BFS 최단거리 |
| 연결 요소의 개수 | [백준 11724](https://www.acmicpc.net/problem/11724) | DFS/BFS 연결 요소 |
| 토마토 | [백준 7569](https://www.acmicpc.net/problem/7569) | 멀티 소스 BFS |
| 단지번호붙이기 | [백준 2667](https://www.acmicpc.net/problem/2667) | DFS/BFS 영역 탐색 |
