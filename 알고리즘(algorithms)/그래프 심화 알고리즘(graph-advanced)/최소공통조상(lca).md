# LCA (Lowest Common Ancestor, 최소 공통 조상)

> 한 줄 정리: 트리에서 두 노드의 가장 가까운 공통 조상을 찾는 알고리즘

## 1. 언제 쓰는가
- 트리에서 두 노드의 공통 조상을 구할 때
- 두 노드 사이의 거리를 구할 때
- 트리에서 경로 쿼리가 많을 때

## 2. 핵심 아이디어
- 기본: 두 노드의 깊이를 맞추고 한 칸씩 올라가며 비교 → O(N)
- 희소 테이블(Sparse Table): 2^k번째 조상을 미리 저장 → O(log N)
- depth[v]: 루트에서 v까지의 깊이
- parent[v][k]: v의 2^k번째 조상

## 3. 시간/공간 복잡도
| 방법 | 전처리 | 쿼리 | 공간 |
|------|--------|------|------|
| 기본 (한 칸씩) | O(N) | O(N) | O(N) |
| 희소 테이블 | O(N log N) | O(log N) | O(N log N) |

## 4. 기본 코드 (Python)

### 기본 구현 (O(N) 쿼리)
```python
import sys
from collections import deque
sys.setrecursionlimit(100000)

def bfs(root, graph, N):
    depth = [-1] * (N + 1)
    parent = [0] * (N + 1)
    depth[root] = 0
    queue = deque([root])

    while queue:
        node = queue.popleft()
        for neighbor in graph[node]:
            if depth[neighbor] == -1:
                depth[neighbor] = depth[node] + 1
                parent[neighbor] = node
                queue.append(neighbor)

    return depth, parent

def lca_basic(u, v, depth, parent):
    # 깊이 맞추기
    while depth[u] > depth[v]:
        u = parent[u]
    while depth[v] > depth[u]:
        v = parent[v]
    # 같은 노드 만날 때까지 올라가기
    while u != v:
        u = parent[u]
        v = parent[v]
    return u
```

### 희소 테이블 구현 (O(log N) 쿼리)
```python
import sys
from collections import deque
import math

def preprocess(root, graph, N):
    LOG = int(math.log2(N)) + 1
    depth = [-1] * (N + 1)
    parent = [[0] * (N + 1) for _ in range(LOG)]

    # BFS로 깊이와 직접 부모 설정
    depth[root] = 0
    queue = deque([root])
    while queue:
        node = queue.popleft()
        for neighbor in graph[node]:
            if depth[neighbor] == -1:
                depth[neighbor] = depth[node] + 1
                parent[0][neighbor] = node
                queue.append(neighbor)

    # 희소 테이블 채우기
    # parent[k][v] = v의 2^k번째 조상
    for k in range(1, LOG):
        for v in range(1, N + 1):
            parent[k][v] = parent[k-1][parent[k-1][v]]

    return depth, parent, LOG

def lca(u, v, depth, parent, LOG):
    # u가 더 깊도록
    if depth[u] < depth[v]:
        u, v = v, u

    # 깊이 맞추기 (2^k씩 올라가기)
    diff = depth[u] - depth[v]
    for k in range(LOG):
        if diff & (1 << k):
            u = parent[k][u]

    if u == v:
        return u

    # 같이 올라가기
    for k in range(LOG - 1, -1, -1):
        if parent[k][u] != parent[k][v]:
            u = parent[k][u]
            v = parent[k][v]

    return parent[0][u]

# 사용 예시
from collections import defaultdict

N = int(input())
graph = defaultdict(list)
for _ in range(N - 1):
    u, v = map(int, input().split())
    graph[u].append(v)
    graph[v].append(u)

depth, parent, LOG = preprocess(1, graph, N)

# 두 노드의 LCA
print(lca(3, 5, depth, parent, LOG))
```

### 두 노드 사이의 거리
```python
def distance(u, v, depth, parent, LOG):
    ancestor = lca(u, v, depth, parent, LOG)
    return depth[u] + depth[v] - 2 * depth[ancestor]
```

## 5. 이걸 떠올려야 할 때
- "트리에서 두 노드의 공통 조상" → LCA
- "트리에서 두 노드 사이의 거리" → LCA로 dist[u] + dist[v] - 2 * dist[lca]
- 쿼리가 많으면 → 희소 테이블 O(log N)
- 쿼리가 적으면 → 기본 구현 O(N)으로 충분

## 6. 자주 틀리는 포인트
- LOG 크기를 `int(log2(N)) + 1`로 잡아야 안전
- 희소 테이블에서 깊이 맞출 때 diff 비트를 낮은 자리부터 체크
- 같이 올라갈 때 `parent[k][u] != parent[k][v]` 조건 → 같아지기 직전까지만
- 루트의 부모는 0 또는 루트 자신으로 초기화
- 1-indexed 노드 번호 확인

## 7. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| LCA | [백준 11437](https://www.acmicpc.net/problem/11437) | LCA 기본 O(N) |
| LCA 2 | [백준 11438](https://www.acmicpc.net/problem/11438) | 희소 테이블 O(log N) |
| 두 스택 | [백준 1854](https://www.acmicpc.net/problem/1854) | LCA 응용 |
| 트리의 지름 | [백준 1167](https://www.acmicpc.net/problem/1167) | LCA + 거리 |
