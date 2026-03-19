# Queue (큐)

> 한 줄 정리: 먼저 넣은 것을 먼저 꺼내는 구조 (FIFO)

## 1. 언제 쓰는가
- FIFO(First In First Out) 구조가 필요할 때
- BFS 구현할 때
- 순서대로 처리해야 할 때
- 최단 거리/최소 횟수 문제
- 여러 작업을 순서대로 처리하는 시뮬레이션

## 2. 핵심 아이디어
- 가장 먼저 넣은 데이터를 먼저 꺼낸다.
- enqueue → 삽입 (`append`)
- dequeue → 제거 (`popleft`)
- Python에서는 `collections.deque` 사용 (list 쓰면 안 됨)

## 3. 시간복잡도
- enqueue: O(1)
- dequeue: O(1) → deque 사용 시
- dequeue: O(n) → list 사용 시 (pop(0)은 느림)

## 4. 기본 코드 (Python)
```python
from collections import deque

queue = deque()

# enqueue
queue.append(1)
queue.append(2)

# dequeue
front = queue.popleft()

# front 확인
front = queue[0]

# empty 체크
if not queue:
    print("empty")
```

## 5. 실전 패턴

### BFS 기본
```python
from collections import deque

def bfs(graph, start):
    queue = deque([start])
    visited = set([start])

    while queue:
        node = queue.popleft()
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

### 2차원 배열 BFS
```python
from collections import deque

dx = [0, 0, 1, -1]
dy = [1, -1, 0, 0]

def bfs(grid, sx, sy):
    queue = deque([(sx, sy)])
    visited = [[False] * len(grid[0]) for _ in range(len(grid))]
    visited[sx][sy] = True

    while queue:
        x, y = queue.popleft()
        for i in range(4):
            nx, ny = x + dx[i], y + dy[i]
            if 0 <= nx < len(grid) and 0 <= ny < len(grid[0]) and not visited[nx][ny]:
                visited[nx][ny] = True
                queue.append((nx, ny))
```

## 6. 이걸 떠올려야 할 때
- "최단 거리/최소 횟수를 구하라" → BFS
- "레벨별로 처리해야 한다" → BFS
- "순서대로 처리해야 한다" → 큐
- "동시에 여러 시작점에서 퍼져나간다" → 멀티 소스 BFS (큐에 시작점 여러 개 넣기)

## 7. 자주 틀리는 포인트
- `list.pop(0)` 쓰면 O(n)이라 시간초과 남 → 반드시 `deque` 써야 함
- visited 체크를 dequeue할 때 하면 같은 노드가 큐에 중복으로 쌓임 → enqueue할 때 바로 체크해야 함
- 2차원 BFS에서 범위 체크 순서 틀리면 IndexError → `0 <= nx < N and 0 <= ny < M` 먼저
- 거리/횟수 셀 때 dist 변수 안 들고 다니고 별도 배열로 관리하는 게 더 깔끔할 때 있음

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| BFS | [백준 1260](https://www.acmicpc.net/problem/1260) | BFS/DFS 기본 |
| 미로 탈출 | [백준 2178](https://www.acmicpc.net/problem/2178) | BFS 최단거리 입문 |
| 토마토 | [백준 7569](https://www.acmicpc.net/problem/7569) | 멀티 소스 BFS |
| 숨바꼭질 | [백준 1697](https://www.acmicpc.net/problem/1697) | 1차원 BFS |
