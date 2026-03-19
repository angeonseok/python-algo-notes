# Greedy (그리디)

> 한 줄 정리: 매 순간 가장 좋아 보이는 선택을 하는 방법, 전체 최적이 보장될 때만 쓸 수 있다

## 1. 언제 쓰는가
- 매 선택이 이후 선택에 영향을 주지 않을 때
- 부분 최적 = 전체 최적이 성립할 때
- 정렬 후 순서대로 처리하면 풀리는 문제
- 교환 논증(exchange argument)으로 증명 가능한 경우

## 2. 핵심 아이디어
- 그리디가 맞는지 확인하는 게 핵심 → 틀릴 수도 있음
- "지금 당장 최선"이 "전체 최선"이 되는 구조인지 따져야 함
- 보통 정렬이 선행됨
- 증명이 어렵다면 DP로 풀 수 있는지 먼저 고민

## 3. 시간복잡도
- 대부분 O(N log N) → 정렬 비용이 지배
- 정렬 후 한 번 순회: O(N log N + N) = O(N log N)

## 4. 기본 코드 (Python)

### 그리디 기본 구조
```python
arr.sort(key=lambda x: ...)  # 기준 정렬

result = 0
for item in arr:
    if 조건:
        result += 1  # 또는 선택
```

### 거스름돈 문제
```python
# 큰 단위부터 최대한 사용
coins = [500, 100, 50, 10]
change = 1260
count = 0

for coin in coins:
    count += change // coin
    change %= coin

print(count)  # 6
```

### 회의실 배정 (대표 유형)
```python
# 끝나는 시간 기준 정렬 → 가장 일찍 끝나는 것부터 선택
meetings = [(1, 4), (3, 5), (0, 6), (5, 7), (3, 8), (5, 9), (6, 10)]
meetings.sort(key=lambda x: (x[1], x[0]))

count = 0
end_time = 0
for start, end in meetings:
    if start >= end_time:
        count += 1
        end_time = end

print(count)  # 4
```

### 최소 횟수로 구간 커버
```python
# 각 구간을 정렬 후 최소 개수로 전체 커버
def min_cover(intervals, total):
    intervals.sort()
    count = 0
    cur_end = 0
    i = 0

    while cur_end < total:
        next_end = cur_end
        while i < len(intervals) and intervals[i][0] <= cur_end:
            next_end = max(next_end, intervals[i][1])
            i += 1
        if next_end == cur_end:
            return -1  # 커버 불가
        cur_end = next_end
        count += 1

    return count
```

## 5. 그리디가 맞는지 판단하는 법
```
1. "매 선택이 독립적인가?" → 이전 선택이 이후에 영향 없으면 그리디 가능
2. "반례가 있는가?" → 작은 예시로 직접 반례 찾아보기
3. "교환해도 손해 없는가?" → 두 선택의 순서를 바꿔도 결과가 같거나 더 좋으면 그리디 OK
4. 반례가 보이면 → DP 고려
```

## 6. 이걸 떠올려야 할 때
- "최소 횟수", "최소 비용"으로 뭔가를 처리 → 그리디 시도
- 정렬 기준이 명확하게 보일 때 → 그리디
- "~를 최대한 많이" → 그리디
- 반례가 바로 떠오르면 → DP로 전환

## 7. 자주 틀리는 포인트
- 그리디가 항상 맞다고 착각 → 반례 먼저 찾아보는 습관
- 정렬 기준을 잘못 잡으면 틀림 → 회의실 배정에서 시작 시간 기준으로 정렬하면 오답
- 그리디로 풀리는 것 같아도 DP가 맞는 경우 있음 → 확신 없으면 작은 케이스로 검증
- 동률일 때 처리 빠뜨리는 경우 → 정렬 key 두 번째 조건까지 챙기기

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 거스름돈 | [백준 5585](https://www.acmicpc.net/problem/5585) | 그리디 기본 |
| 회의실 배정 | [백준 1931](https://www.acmicpc.net/problem/1931) | 그리디 대표 유형 |
| 강의실 배정 | [백준 11000](https://www.acmicpc.net/problem/11000) | 그리디 + 힙 |
| 잃어버린 괄호 | [백준 1541](https://www.acmicpc.net/problem/1541) | 그리디 사고력 |
| 주유소 | [백준 13305](https://www.acmicpc.net/problem/13305) | 그리디 응용 |
