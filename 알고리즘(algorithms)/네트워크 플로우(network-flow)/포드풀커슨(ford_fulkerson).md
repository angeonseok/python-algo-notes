# Ford-Fulkerson (포드-풀커슨)

> 한 줄 정리: 소스에서 싱크까지 증가 경로를 찾아 흐름을 반복적으로 늘리는 최대 유량 알고리즘

## 1. 언제 쓰는가
- 네트워크에서 최대 유량을 구할 때
- 에드몬즈-카프, 디닉의 기반 개념 이해용
- 용량이 정수이고 최대 유량이 작을 때

## 2. 핵심 아이디어
- 소스(S) → 싱크(T)로 흐를 수 있는 최대 유량 계산
- 증가 경로(augmenting path): 잔여 용량이 남은 경로
- 역방향 간선: 흐름을 취소할 수 있도록 반대 방향 간선 유지
- 증가 경로가 없을 때까지 반복

## 3. 시간복잡도
| | 복잡도 |
|--|--------|
| 시간 | O(E × F) (F = 최대 유량) |
| 문제 | F가 크면 매우 느림 |

## 4. 기본 코드 (Python)

### 포드-풀커슨 (DFS 기반)
```python
import sys
from collections import defaultdict
sys.setrecursionlimit(100000)

def ford_fulkerson(graph, capacity, source, sink, V):
    def dfs(v, visited, flow):
        if v == sink:
            return flow
        visited.add(v)
        for u in graph[v]:
            if u not in visited and capacity[v][u] > 0:
                result = dfs(u, visited, min(flow, capacity[v][u]))
                if result > 0:
                    capacity[v][u] -= result
                    capacity[u][v] += result  # 역방향 간선
                    return result
        return 0

    total = 0
    while True:
        flow = dfs(source, set(), float('inf'))
        if flow == 0:
            break
        total += flow

    return total

# 입력 예시
V, E = map(int, input().split())
graph = defaultdict(list)
capacity = defaultdict(lambda: defaultdict(int))

for _ in range(E):
    u, v, c = map(int, input().split())
    graph[u].append(v)
    graph[v].append(u)  # 역방향 간선 경로
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
- 네트워크 플로우 개념 이해용
- 최대 유량 F가 작고 간선 수도 적을 때
- 실전에서는 에드몬즈-카프 또는 디닉 사용 권장

## 7. 자주 틀리는 포인트
- 역방향 간선 빠뜨리면 최적 유량 못 찾음
- F가 크면 O(EF)라 시간초과 → 에드몬즈-카프로 대체
- 같은 간선이 중복 입력될 때 용량 합산 처리

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 최대 유량 | [백준 6086](https://www.acmicpc.net/problem/6086) | 네트워크 플로우 기본 |
