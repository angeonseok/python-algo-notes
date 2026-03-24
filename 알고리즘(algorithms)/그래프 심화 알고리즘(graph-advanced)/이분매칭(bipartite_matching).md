# Bipartite Matching (이분 매칭)

> 한 줄 정리: 두 그룹 사이에서 최대한 많은 쌍을 매칭하는 알고리즘

## 1. 언제 쓰는가
- 두 집합 사이의 최대 매칭 수를 구할 때
- 일대일 대응이 가능한 최대 쌍의 수
- 작업-사람 배정, 좌석 배정 문제
- 최소 버텍스 커버 (쾨닉 정리)

## 2. 핵심 아이디어
- 이분 그래프: 정점을 두 그룹으로 나눌 수 있고 간선이 그룹 사이에만 존재
- DFS로 증가 경로를 찾아 매칭을 하나씩 늘림
- 이미 매칭된 정점도 다른 경로가 있으면 재배정 가능
- 시간복잡도: O(V * E)

## 3. 시간복잡도
| | 복잡도 |
|--|--------|
| 시간 | O(V × E) |
| 공간 | O(V + E) |

## 4. 기본 코드 (Python)

### 이분 매칭 기본 구현
```python
from collections import defaultdict

def bipartite_matching(graph, left_size, right_size):
    match_left  = [-1] * (left_size + 1)   # match_left[i]  = i와 매칭된 오른쪽 정점
    match_right = [-1] * (right_size + 1)  # match_right[j] = j와 매칭된 왼쪽 정점

    def dfs(v, visited):
        for u in graph[v]:
            if visited[u]:
                continue
            visited[u] = True
            # u가 매칭 안 됐거나, u의 매칭 상대가 다른 곳으로 갈 수 있으면
            if match_right[u] == -1 or dfs(match_right[u], visited):
                match_left[v] = u
                match_right[u] = v
                return True
        return False

    result = 0
    for v in range(1, left_size + 1):
        visited = [False] * (right_size + 1)
        if dfs(v, visited):
            result += 1

    return result, match_left, match_right

# 입력 예시
left_size, right_size, E = map(int, input().split())
graph = defaultdict(list)

for _ in range(E):
    u, v = map(int, input().split())
    graph[u].append(v)

count, match_left, match_right = bipartite_matching(graph, left_size, right_size)
print(count)  # 최대 매칭 수
```

## 5. 실전 패턴

### 매칭 결과 확인
```python
# 매칭된 쌍 출력
for i in range(1, left_size + 1):
    if match_left[i] != -1:
        print(f"{i} - {match_left[i]}")
```

### 이분 그래프 판별
```python
from collections import deque

def is_bipartite(graph, V):
    color = [-1] * (V + 1)

    for start in range(1, V + 1):
        if color[start] != -1:
            continue
        color[start] = 0
        queue = deque([start])

        while queue:
            v = queue.popleft()
            for neighbor in graph[v]:
                if color[neighbor] == -1:
                    color[neighbor] = 1 - color[v]
                    queue.append(neighbor)
                elif color[neighbor] == color[v]:
                    return False  # 같은 색 → 이분 그래프 아님

    return True
```

## 6. 이걸 떠올려야 할 때
- "두 그룹 사이 최대 매칭" → 이분 매칭
- "사람-작업 최대 배정" → 이분 매칭
- "최소 버텍스 커버" → 최대 매칭 수와 같음 (쾨닉 정리)
- "이분 그래프인지 판별" → BFS로 2-색칠

## 7. 자주 틀리는 포인트
- visited 배열을 매 DFS마다 초기화해야 함 → for 루프 안에서 선언
- match_right 재배정 가능 여부 체크 빠뜨리면 최적 매칭 못 찾음
- 이분 그래프가 아닌 그래프에 적용하면 오답
- left/right 인덱스 범위 혼용 주의

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 이분 매칭 | [백준 11375](https://www.acmicpc.net/problem/11375) | 이분 매칭 기본 |
| 열혈강호 | [백준 11376](https://www.acmicpc.net/problem/11376) | 이분 매칭 응용 |
| 축구 전술 | [백준 15711](https://www.acmicpc.net/problem/15711) | 이분 그래프 판별 |
