# Floyd-Warshall (플로이드 워셜)

> 한 줄 정리: 모든 정점 쌍 사이의 최단 거리를 한 번에 구한다

## 1. 언제 쓰는가
- 모든 정점 쌍의 최단 거리가 필요할 때
- 정점 수가 적을 때 (V ≤ 500)
- 음수 가중치 있어도 사용 가능 (음수 사이클 없을 때)
- 경유지를 거치는 최단 경로

## 2. 핵심 아이디어
- k를 경유지로 사용할 때 i → j 최단 거리 갱신
- dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])
- 3중 반복문으로 구현, k(경유지)가 가장 바깥 루프
- 인접 행렬로 구현

## 3. 시간/공간 복잡도
| | 복잡도 |
|--|--------|
| 시간 | O(V³) |
| 공간 | O(V²) |

## 4. 기본 코드 (Python)

### 플로이드 워셜 기본
```python
import sys

def floyd_warshall(V, edges):
    INF = sys.maxsize // 2  # 오버플로 방지
    dist = [[INF] * (V + 1) for _ in range(V + 1)]

    # 자기 자신은 0
    for i in range(V + 1):
        dist[i][i] = 0

    # 간선 초기화
    for u, v, w in edges:
        dist[u][v] = min(dist[u][v], w)  # 중복 간선 처리

    # 플로이드 워셜
    for k in range(1, V + 1):       # 경유지
        for i in range(1, V + 1):   # 출발
            for j in range(1, V + 1):  # 도착
                if dist[i][k] + dist[k][j] < dist[i][j]:
                    dist[i][j] = dist[i][k] + dist[k][j]

    return dist

# 음수 사이클 감지
def has_negative_cycle(dist, V):
    for i in range(1, V + 1):
        if dist[i][i] < 0:
            return True
    return False
```

## 5. 세 알고리즘 비교
| | 다익스트라 | 벨만포드 | 플로이드 워셜 |
|--|-----------|---------|-------------|
| 출발점 | 단일 | 단일 | 모든 쌍 |
| 음수 가중치 | 불가 | 가능 | 가능 |
| 음수 사이클 감지 | 불가 | 가능 | 가능 |
| 시간복잡도 | O((V+E) log V) | O(VE) | O(V³) |
| 적합한 V 크기 | 대규모 | 중간 | V ≤ 500 |

## 6. 이걸 떠올려야 할 때
- "모든 정점 쌍 최단 거리" → 플로이드 워셜
- V가 작고 (≤ 500) 쿼리가 많을 때 → 플로이드 워셜로 전처리
- "A에서 B를 거쳐 C로 가는 최단 거리" → dist[A][B] + dist[B][C]

## 7. 자주 틀리는 포인트
- k 루프가 가장 바깥에 있어야 함 → 안쪽에 두면 오답
- INF + INF 오버플로 → `INF = sys.maxsize // 2` 사용
- 중복 간선 있을 때 최솟값으로 초기화 안 하면 오답
- 양방향 그래프면 dist[u][v]와 dist[v][u] 모두 초기화

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 플로이드 | [백준 11404](https://www.acmicpc.net/problem/11404) | 플로이드 워셜 기본 |
| 경로 찾기 | [백준 11403](https://www.acmicpc.net/problem/11403) | 플로이드 워셜 응용 |
| 케빈 베이컨의 6단계 법칙 | [백준 1389](https://www.acmicpc.net/problem/1389) | 모든 쌍 최단 거리 |
