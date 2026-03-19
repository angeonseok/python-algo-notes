# Tree (트리)

> 한 줄 정리: 계층적 관계를 표현하는 자료구조, 사이클 없는 연결 그래프

## 1. 언제 쓰는가
- 계층 구조를 표현해야 할 때 (폴더, 조직도)
- 부모-자식 관계가 있는 데이터
- 탐색/삽입/삭제를 빠르게 해야 할 때 (BST)
- 계층적 분류가 필요한 경우

## 2. 핵심 개념 / 용어
```
        1          ← 루트 (root): 최상위 노드
       / \
      2   3        ← 내부 노드 (internal node)
     / \   \
    4   5   6      ← 리프 (leaf): 자식 없는 노드
```
- **노드 (Node)**: 트리의 각 원소
- **루트 (Root)**: 최상위 노드, 부모 없음
- **리프 (Leaf)**: 자식이 없는 노드
- **부모 / 자식 (Parent / Child)**: 바로 위/아래 노드
- **형제 (Sibling)**: 같은 부모를 가진 노드
- **깊이 (Depth)**: 루트에서 해당 노드까지의 거리
- **높이 (Height)**: 해당 노드에서 가장 깊은 리프까지의 거리
- **레벨 (Level)**: 깊이 + 1 (루트가 1레벨)
- **차수 (Degree)**: 노드의 자식 수
- **서브트리 (Subtree)**: 특정 노드를 루트로 하는 트리

## 3. 시간복잡도
| 연산 | 일반 트리 | BST 평균 | BST 최악 |
|------|-----------|----------|----------|
| 탐색 | O(n) | O(log n) | O(n) |
| 삽입 | O(n) | O(log n) | O(n) |
| 삭제 | O(n) | O(log n) | O(n) |

## 4. 기본 코드 (Python)

### 노드 구현
```python
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None  # 이진 트리 기준
```

### 코테에서 자주 쓰는 인접 리스트 방식
```python
import sys
from collections import defaultdict
input = sys.stdin.readline

N = int(input())
tree = defaultdict(list)

for _ in range(N - 1):
    u, v = map(int, input().split())
    tree[u].append(v)
    tree[v].append(u)  # 무방향
```

### 루트 기준 부모/자식 관계 정리 (BFS)
```python
from collections import deque

def build_tree(tree, root, N):
    parent = [-1] * (N + 1)
    children = defaultdict(list)
    visited = [False] * (N + 1)

    queue = deque([root])
    visited[root] = True

    while queue:
        node = queue.popleft()
        for neighbor in tree[node]:
            if not visited[neighbor]:
                visited[neighbor] = True
                parent[neighbor] = node
                children[node].append(neighbor)
                queue.append(neighbor)

    return parent, children
```

## 5. 이걸 떠올려야 할 때
- "계층 구조", "부모-자식 관계" → 트리
- "사이클 없는 연결 그래프" → 트리랑 동일
- 노드 N개짜리 트리의 간선은 항상 N-1개
- 루트 없이 주어지면 보통 1번 노드가 루트

## 6. 자주 틀리는 포인트
- 트리도 그래프라서 인접 리스트로 입력받고 visited 체크해야 함
- 높이(height)와 깊이(depth) 헷갈리는 경우 → 높이는 아래에서, 깊이는 위에서
- 루트가 명시 안 되면 양방향으로 받아두고 탐색 시작점 지정
- 간선 수 = N - 1 인 걸 활용하면 입력 처리 편함

## 7. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 트리 순회 | [백준 1991](https://www.acmicpc.net/problem/1991) | 전위/중위/후위 기본 |
| 트리의 부모 찾기 | [백준 11725](https://www.acmicpc.net/problem/11725) | BFS로 부모 관계 정리 |
| 트리의 지름 | [백준 1167](https://www.acmicpc.net/problem/1167) | DFS 두 번 |

## 9. 트리 순회 (Tree Traversal)

> 트리의 모든 노드를 방문하는 방법, 방문 순서에 따라 세 가지로 나뉨

### 전위 순회 (Preorder): 루트 → 왼 → 오
```python
def preorder(node):
    if not node:
        return
    print(node.val)   # 루트 먼저
    preorder(node.left)
    preorder(node.right)
```

### 중위 순회 (Inorder): 왼 → 루트 → 오
```python
def inorder(node):
    if not node:
        return
    inorder(node.left)
    print(node.val)   # 중간에 루트
    inorder(node.right)
```
- BST에서 중위 순회하면 오름차순 정렬된 결과가 나옴

### 후위 순회 (Postorder): 왼 → 오 → 루트
```python
def postorder(node):
    if not node:
        return
    postorder(node.left)
    postorder(node.right)
    print(node.val)   # 루트 마지막
```
- 자식을 먼저 처리해야 할 때 (폴더 삭제, 수식 계산 등)

### 레벨 순회 (Level Order): BFS
```python
from collections import deque

def level_order(root):
    if not root:
        return
    queue = deque([root])
    while queue:
        node = queue.popleft()
        print(node.val)
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
```

### 헷갈릴 때 기억법
```
전위: 루트가 제일 앞  → 루트-왼-오
중위: 루트가 중간     → 왼-루트-오
후위: 루트가 제일 뒤  → 왼-오-루트
```
