# LIS (Longest Increasing Subsequence, 최장 증가 부분 수열)

> 한 줄 정리: 수열에서 값이 증가하는 가장 긴 부분 수열의 길이

## 1. 언제 쓰는가
- 수열에서 증가하는 가장 긴 부분 수열을 구할 때
- O(N²) DP로 기본 구현, O(N log N)으로 최적화 가능
- 인내심 게임, 박스 쌓기 등 변형 문제

## 2. 핵심 아이디어
- dp[i] = i번째 원소로 끝나는 LIS의 길이
- 모든 j < i에 대해 arr[j] < arr[i]이면 dp[i] = max(dp[i], dp[j] + 1)
- O(N log N): 이진 탐색으로 LIS 배열을 유지

## 3. 시간/공간 복잡도
| 방법 | 시간 | 공간 |
|------|------|------|
| DP | O(N²) | O(N) |
| DP + 이진 탐색 | O(N log N) | O(N) |

## 4. 기본 코드 (Python)

### O(N²) DP
```python
def lis(arr):
    N = len(arr)
    dp = [1] * N

    for i in range(1, N):
        for j in range(i):
            if arr[j] < arr[i]:
                dp[i] = max(dp[i], dp[j] + 1)

    return max(dp)
```

### O(N log N) 이진 탐색
```python
import bisect

def lis(arr):
    tails = []  # tails[i] = 길이 i+1인 LIS의 마지막 원소 중 최솟값

    for v in arr:
        idx = bisect.bisect_left(tails, v)
        if idx == len(tails):
            tails.append(v)
        else:
            tails[idx] = v  # 더 작은 값으로 교체 (실제 수열은 아님)

    return len(tails)
```

### LIS 실제 수열 복원 (O(N log N))
```python
import bisect

def lis_sequence(arr):
    N = len(arr)
    tails = []
    indices = []   # 각 원소가 삽입된 tails 인덱스
    predecessors = [-1] * N  # 이전 원소 인덱스

    for i, v in enumerate(arr):
        idx = bisect.bisect_left(tails, v)
        if idx == len(tails):
            tails.append(v)
        else:
            tails[idx] = v
        indices.append(idx)
        if idx > 0:
            # 이전 단계에서 idx-1 위치에 있던 원소 찾기
            for j in range(i - 1, -1, -1):
                if indices[j] == idx - 1 and arr[j] < v:
                    predecessors[i] = j
                    break

    # 역추적
    result = []
    idx = len(tails) - 1
    for i in range(N - 1, -1, -1):
        if indices[i] == idx:
            result.append(arr[i])
            idx -= 1
            if idx < 0:
                break

    return list(reversed(result))
```

## 5. 이걸 떠올려야 할 때
- "증가하는 부분 수열의 최대 길이" → LIS
- N ≤ 1000이면 O(N²) DP로 충분
- N > 10000이면 O(N log N) 필요
- "박스 쌓기", "인내심 게임" 유형 → LIS 변형

## 6. 자주 틀리는 포인트
- O(N log N)의 tails 배열은 실제 LIS가 아님 → 수열 복원 시 별도 처리 필요
- 비감소(≥) vs 순증가(>) 조건 혼동 → `bisect_left` vs `bisect_right` 선택
- dp 초기값을 0이 아닌 1로 설정 (자기 자신 포함)

## 7. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 가장 긴 증가하는 부분 수열 | [백준 11053](https://www.acmicpc.net/problem/11053) | LIS O(N²) |
| 가장 긴 증가하는 부분 수열 4 | [백준 14002](https://www.acmicpc.net/problem/14002) | LIS 수열 복원 |
| 가장 긴 증가하는 부분 수열 5 | [백준 14003](https://www.acmicpc.net/problem/14003) | LIS O(N log N) |
| 인내심 게임 | [백준 17595](https://www.acmicpc.net/problem/17595) | LIS 변형 |
