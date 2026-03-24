# Sweeping (스위핑)

> 한 줄 정리: 좌표를 한 방향으로 훑으면서 이벤트를 처리하는 방법, 정렬 후 순서대로 처리한다

## 1. 언제 쓰는가
- 겹치는 구간의 최대 개수를 구할 때
- 구간들의 합집합 길이를 구할 때
- 좌표 기반 문제에서 불필요한 탐색을 줄일 때
- 회의실 배정, 주차장 문제 등 시간/구간 관련 문제

## 2. 핵심 아이디어
- 이벤트(시작/끝)를 좌표 기준으로 정렬
- 왼쪽에서 오른쪽으로 한 번만 훑으며 처리
- 시작점 +1, 끝점 -1로 구간을 이벤트로 변환
- 정렬이 선행되어야 함

## 3. 시간복잡도
| | 복잡도 |
|--|--------|
| 시간 | O(N log N) (정렬이 지배) |
| 공간 | O(N) |

## 4. 기본 코드 (Python)

### 구간 겹침 최대 개수
```python
def max_overlap(intervals):
    events = []
    for start, end in intervals:
        events.append((start, 1))   # 시작: +1
        events.append((end, -1))    # 끝: -1

    # 같은 좌표면 끝(-1)을 먼저 처리 (끝과 시작이 같은 점에서 만날 때)
    events.sort(key=lambda x: (x[0], x[1]))

    max_count = 0
    count = 0
    for _, delta in events:
        count += delta
        max_count = max(max_count, count)

    return max_count
```

### 구간 합집합 길이
```python
def union_length(intervals):
    if not intervals:
        return 0

    intervals.sort()
    total = 0
    cur_start, cur_end = intervals[0]

    for start, end in intervals[1:]:
        if start <= cur_end:            # 겹치면 합치기
            cur_end = max(cur_end, end)
        else:                           # 안 겹치면 확정
            total += cur_end - cur_start
            cur_start, cur_end = start, end

    total += cur_end - cur_start
    return total
```

### 차이 배열 스위핑 (구간 업데이트)
```python
def sweeping_diff(n, updates):
    # updates: [(l, r, val), ...]
    # l~r 구간에 val 더하기를 O(1)에 처리
    diff = [0] * (n + 2)

    for l, r, val in updates:
        diff[l] += val
        diff[r + 1] -= val

    # 실제 배열 복원
    result = []
    cur = 0
    for i in range(1, n + 1):
        cur += diff[i]
        result.append(cur)

    return result
```

### 좌표 압축 + 스위핑
```python
def sweeping_with_compression(intervals):
    # 좌표가 매우 클 때 압축 후 스위핑
    coords = sorted(set(x for s, e in intervals for x in (s, e)))
    rank = {v: i for i, v in enumerate(coords)}

    events = []
    for start, end in intervals:
        events.append((rank[start], 1))
        events.append((rank[end], -1))

    events.sort(key=lambda x: (x[0], x[1]))

    count = 0
    max_count = 0
    for _, delta in events:
        count += delta
        max_count = max(max_count, count)

    return max_count
```

## 5. 이걸 떠올려야 할 때
- "겹치는 구간의 최대 개수" → 시작/끝 이벤트 스위핑
- "구간들의 합집합 길이" → 정렬 후 병합 스위핑
- "구간에 값을 더하는 연산이 많다" → 차이 배열 스위핑
- "좌표가 너무 크다" → 좌표 압축 후 스위핑

## 6. 자주 틀리는 포인트
- 같은 좌표에서 시작과 끝이 만날 때 정렬 기준 → 끝(-1)을 먼저 처리할지 나중에 처리할지 문제에 따라 다름
- 구간 합집합에서 `cur_end = max(cur_end, end)` 빠뜨리면 오답 (완전히 포함된 구간 처리)
- 차이 배열에서 `diff[r+1]` 범위 초과 → 크기를 n+2로 선언

## 7. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 회의실 배정 | [백준 1931](https://www.acmicpc.net/problem/1931) | 스위핑 기본 |
| 주차장 | [백준 2170](https://www.acmicpc.net/problem/2170) | 구간 합집합 |
| 겹치는 선분 | [백준 1689](https://www.acmicpc.net/problem/1689) | 구간 겹침 최대 |
| 나무 심기 | [백준 1280](https://www.acmicpc.net/problem/1280) | 스위핑 응용 |
