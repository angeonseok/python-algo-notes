# Heap Sort (힙 정렬)

> 한 줄 정리: 힙 자료구조를 이용한 정렬, 항상 O(n log n)이고 추가 메모리가 필요 없다

## 1. 언제 쓰는가
- 추가 메모리 없이 O(n log n) 보장이 필요할 때
- 코테에서 직접 쓸 일은 거의 없지만 힙 개념과 연결해서 이해
- "K개의 최솟값/최댓값만 필요" → heapq로 대체

## 2. 핵심 아이디어
- 전체를 최대 힙으로 만들기 (heapify)
- 루트(최댓값)를 맨 뒤로 보내고 힙 크기 줄이기
- 반복하면 오름차순 정렬 완성
- Python `heapq`는 최소 힙이라 힙 정렬엔 직접 구현 필요

## 3. 시간/공간 복잡도
| 케이스 | 시간복잡도 |
|--------|-----------|
| 최선 | O(n log n) |
| 평균 | O(n log n) |
| 최악 | O(n log n) |
| 공간복잡도 | O(1) |

- unstable 정렬 (같은 값의 순서가 바뀔 수 있음)

## 4. 기본 코드 (Python)

### 기본형
```python
def heap_sort(arr):
    n = len(arr)

    # 최대 힙 구성 (뒤쪽 내부 노드부터 heapify)
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)

    # 루트(최댓값)를 맨 뒤로 보내며 정렬
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]  # 최댓값을 맨 뒤로
        heapify(arr, i, 0)               # 줄어든 힙 재정렬

    return arr

def heapify(arr, n, i):
    largest = i
    left  = 2 * i + 1
    right = 2 * i + 2

    if left < n and arr[left] > arr[largest]:
        largest = left
    if right < n and arr[right] > arr[largest]:
        largest = right

    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)
```

### 코테에서 실제로 쓰는 방식 (heapq)
```python
import heapq

def heap_sort(arr):
    heapq.heapify(arr)
    return [heapq.heappop(arr) for _ in range(len(arr))]
```

## 5. 이걸 떠올려야 할 때
- 코테에서 직접 구현할 일 없음 → `arr.sort()` 쓰기
- 힙 정렬보다 `heapq`로 K개 최솟값 구하는 패턴이 더 자주 등장
- 병합/퀵 정렬과 비교: 추가 메모리 없이 O(n log n) 보장이 필요할 때

## 6. 자주 틀리는 포인트
- `heapify` 시작을 `n // 2 - 1`부터 하는 이유 → 리프 노드는 자식이 없어서 heapify 불필요
- Python `heapq`는 최소 힙 → 최대 힙 구현하려면 직접 짜거나 값에 `-` 붙이기
- unstable 정렬이라 같은 값의 순서 보장 안 됨

## 7. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 수 정렬하기 2 | [백준 2751](https://www.acmicpc.net/problem/2751) | O(n log n) 정렬 |
| 최소 힙 | [백준 1927](https://www.acmicpc.net/problem/1927) | heapq 활용 |
