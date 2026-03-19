# Quick Sort (퀵 정렬)

> 한 줄 정리: 피벗을 기준으로 작은 값/큰 값으로 나눠 재귀 정렬, 평균 O(n log n)으로 실전에서 가장 빠르다

## 1. 언제 쓰는가
- 평균적으로 가장 빠른 정렬이 필요할 때
- 제자리 정렬(추가 메모리 최소화)이 필요할 때
- Python 기본 정렬(Tim Sort)로 대체 가능하지만 개념은 필수

## 2. 핵심 아이디어
- 피벗(pivot) 하나를 선택
- 피벗보다 작은 값은 왼쪽, 큰 값은 오른쪽으로 분리
- 각 부분을 재귀적으로 반복
- 피벗 선택이 성능을 좌우 → 랜덤 or 중앙값 선택이 유리

## 3. 시간/공간 복잡도
| 케이스 | 시간복잡도 |
|--------|-----------|
| 최선 | O(n log n) |
| 평균 | O(n log n) |
| 최악 (이미 정렬됨) | O(n²) |
| 공간복잡도 | O(log n) |

- unstable 정렬 (같은 값의 순서가 바뀔 수 있음)
- 최악을 피하려면 랜덤 피벗 사용

## 4. 기본 코드 (Python)

### 기본형 (Python스러운 방식)
```python
def quick_sort(arr):
    if len(arr) <= 1:
        return arr

    pivot = arr[len(arr) // 2]  # 중앙값 피벗
    left   = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right  = [x for x in arr if x > pivot]

    return quick_sort(left) + middle + quick_sort(right)
```

### 제자리 정렬 (in-place, 추가 메모리 최소화)
```python
import random

def quick_sort(arr, lo, hi):
    if lo >= hi:
        return
    pivot_idx = partition(arr, lo, hi)
    quick_sort(arr, lo, pivot_idx - 1)
    quick_sort(arr, pivot_idx + 1, hi)

def partition(arr, lo, hi):
    # 랜덤 피벗으로 최악 케이스 방지
    rand_idx = random.randint(lo, hi)
    arr[rand_idx], arr[hi] = arr[hi], arr[rand_idx]

    pivot = arr[hi]
    i = lo - 1
    for j in range(lo, hi):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    arr[i + 1], arr[hi] = arr[hi], arr[i + 1]
    return i + 1

# 사용
arr = [3, 1, 4, 1, 5, 9, 2, 6]
quick_sort(arr, 0, len(arr) - 1)
```

## 5. 이걸 떠올려야 할 때
- 코테에서 직접 구현할 일은 적음 → `arr.sort()` 쓰기
- 피벗 선택 전략 문제나 분할 방식 이해가 필요한 문제에서 등장
- "K번째 작은 값 O(n)에 찾기" → Quick Select (퀵 정렬 응용)

## 6. 자주 틀리는 포인트
- 피벗을 항상 첫 번째/마지막으로 고르면 이미 정렬된 배열에서 O(n²) → 랜덤 피벗 필수
- Python 재귀 제한 → `sys.setrecursionlimit` 설정하거나 반복문으로 구현
- 기본형(리스트 컴프리헨션)은 메모리 O(n) 추가 사용

## 7. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 수 정렬하기 2 | [백준 2751](https://www.acmicpc.net/problem/2751) | O(n log n) 정렬 |
| K번째 수 | [백준 11004](https://www.acmicpc.net/problem/11004) | Quick Select 응용 |
