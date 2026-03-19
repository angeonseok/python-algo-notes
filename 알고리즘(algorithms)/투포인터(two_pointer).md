# Two Pointer (투 포인터)

> 한 줄 정리: 두 개의 포인터를 이용해 배열을 O(N)에 탐색하는 방법

## 1. 언제 쓰는가
- 정렬된 배열에서 두 값의 합/차 조건
- 연속된 구간의 합이 특정 값인 경우
- 중복 없는 최장 부분 문자열
- 슬라이딩 윈도우와 함께 사용

## 2. 핵심 아이디어
- 두 포인터가 같은 방향 or 반대 방향으로 이동
- 조건에 따라 포인터를 한 칸씩 움직임
- O(N²)을 O(N)으로 줄이는 게 핵심

## 3. 시간/공간 복잡도
| | 복잡도 |
|--|--------|
| 시간 | O(N) ~ O(N log N) (정렬 포함) |
| 공간 | O(1) |

## 4. 기본 코드 (Python)

### 반대 방향 (정렬된 배열에서 합)
```python
def two_sum(arr, target):
    arr.sort()
    left, right = 0, len(arr) - 1

    while left < right:
        s = arr[left] + arr[right]
        if s == target:
            return left, right
        elif s < target:
            left += 1
        else:
            right -= 1

    return -1, -1
```

### 같은 방향 (연속 구간 합)
```python
def count_subarrays(arr, target):
    left = 0
    curr_sum = 0
    count = 0

    for right in range(len(arr)):
        curr_sum += arr[right]

        while curr_sum > target and left <= right:
            curr_sum -= arr[left]
            left += 1

        if curr_sum == target:
            count += 1

    return count
```

### 슬라이딩 윈도우 (고정 크기)
```python
def max_sum_window(arr, k):
    window = sum(arr[:k])
    result = window

    for i in range(k, len(arr)):
        window += arr[i] - arr[i - k]
        result = max(result, window)

    return result
```

### 중복 없는 최장 부분 문자열
```python
def longest_unique(s):
    left = 0
    seen = {}
    result = 0

    for right, c in enumerate(s):
        if c in seen and seen[c] >= left:
            left = seen[c] + 1
        seen[c] = right
        result = max(result, right - left + 1)

    return result
```

## 5. 이걸 떠올려야 할 때
- "정렬된 배열 + 두 값의 합/차 조건" → 반대 방향 투 포인터
- "연속 구간의 합" → 같은 방향 투 포인터
- "고정 크기 구간의 최솟값/최댓값" → 슬라이딩 윈도우
- O(N²) 이중 반복문이 시간초과 → 투 포인터 고려

## 6. 자주 틀리는 포인트
- 반대 방향에서 `left < right` 조건 빠뜨리면 같은 원소 중복 사용
- 같은 방향에서 음수가 있으면 투 포인터 불가 → 누적 합 고려
- 슬라이딩 윈도우에서 윈도우 초기값 설정 빠뜨리는 경우

## 7. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 두 수의 합 | [백준 3273](https://www.acmicpc.net/problem/3273) | 반대 방향 투 포인터 |
| 부분합 | [백준 1806](https://www.acmicpc.net/problem/1806) | 같은 방향 투 포인터 |
| 최장 부분 문자열 | [리트코드 3](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | 슬라이딩 윈도우 |
