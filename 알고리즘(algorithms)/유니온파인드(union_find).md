# Union-Find (유니온 파인드 / 서로소 집합)

> 한 줄 정리: 여러 노드의 연결 여부를 빠르게 확인하고 합치는 자료구조

## 1. 언제 쓰는가
- 두 노드가 같은 집합(연결 요소)에 속하는지 확인
- 사이클 감지
- 크루스칼 알고리즘 (MST)
- 동적으로 연결 관계가 추가될 때

## 2. 핵심 아이디어
- Find: 노드의 루트(대표) 찾기
- Union: 두 집합 합치기
- 경로 압축(Path Compression): Find 시 루트를 직접 연결 → O(α(N)) ≈ O(1)
- 랭크 기반 합치기: 트리 높이 최소화

## 3. 시간/공간 복잡도
| 연산 | 복잡도 |
|------|--------|
| Find (경로 압축) | O(α(N)) ≈ O(1) |
| Union | O(α(N)) ≈ O(1) |
| 공간 | O(N) |

- α(N): 아커만 함수의 역함수, 사실상 상수

## 4. 기본 코드 (Python)

### 함수형 구현 (코테 표준)
```python
import sys
sys.setrecursionlimit(100000)

def find(parent, x):
    if parent[x] != x:
        parent[x] = find(parent, parent[x])  # 경로 압축: 루트를 직접 연결
    return parent[x]

def union(parent, x, y):
    rx, ry = find(parent, x), find(parent, y)
    if rx == ry:
        return False  # 이미 같은 집합 → 사이클
    parent[ry] = rx   # ry를 rx 아래로 합치기
    return True

# 초기화 및 사용
parent = list(range(N + 1))  # parent[i] = i (자기 자신이 루트)

union(parent, 1, 2)           # 1, 2 합치기
union(parent, 2, 3)           # 2, 3 합치기
find(parent, 1) == find(parent, 3)  # True (같은 집합)
```

### 경로 압축이 왜 중요한가
```python
# 경로 압축 없는 find → 편향 트리에서 O(N)
def find_slow(parent, x):
    if parent[x] != x:
        return find_slow(parent, parent[x])  # 루트까지 매번 거슬러 올라감
    return x

# 경로 압축 있는 find → O(α(N)) ≈ O(1)
def find(parent, x):
    if parent[x] != x:
        parent[x] = find(parent, parent[x])  # 루트를 직접 연결해버림
    return parent[x]
```

### 반복문 버전 (재귀 깊이 걱정 없음)
```python
def find(parent, x):
    root = x
    while parent[root] != root:  # 루트 찾기
        root = parent[root]
    while parent[x] != root:     # 경로 압축
        parent[x], x = root, parent[x]
    return root
```

### 참고: 클래스 버전 (경로 압축 + 랭크)
```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n + 1))
        self.rank = [0] * (n + 1)

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        rx, ry = self.find(x), self.find(y)
        if rx == ry:
            return False
        if self.rank[rx] < self.rank[ry]:
            rx, ry = ry, rx
        self.parent[ry] = rx
        if self.rank[rx] == self.rank[ry]:
            self.rank[rx] += 1
        return True
```

## 5. 실전 패턴

### 사이클 감지
```python
parent = list(range(V + 1))
has_cycle = False

for u, v in edges:
    if not union(parent, u, v):  # 이미 같은 집합 → 사이클
        has_cycle = True
```

### 연결 요소 개수
```python
parent = list(range(V + 1))
for u, v in edges:
    union(parent, u, v)

# 루트가 자기 자신인 노드 수 = 연결 요소 수
components = len(set(find(parent, i) for i in range(1, V + 1)))
```

## 6. 이걸 떠올려야 할 때
- "두 노드가 연결되어 있는가" → 유니온 파인드
- "사이클 존재 여부" → 유니온 파인드
- "크루스칼 MST" → 유니온 파인드 필수
- 간선이 동적으로 추가될 때 → 유니온 파인드 (BFS/DFS보다 빠름)

## 7. 자주 틀리는 포인트
- 경로 압축 없으면 트리가 편향되어 O(N)으로 느려짐
- `find` 재귀 깊이 → `sys.setrecursionlimit` 설정 or 반복문으로 구현
- union 시 find 결과로 합쳐야 함 → `parent[y] = x` 대신 `parent[ry] = rx`

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 집합의 표현 | [백준 1717](https://www.acmicpc.net/problem/1717) | 유니온 파인드 기본 |
| 사이클 게임 | [백준 20040](https://www.acmicpc.net/problem/20040) | 사이클 감지 |
| 네트워크 연결 | [백준 1922](https://www.acmicpc.net/problem/1922) | 크루스칼 + 유니온 파인드 |
