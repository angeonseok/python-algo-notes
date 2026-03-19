# Merge Sort (병합 정렬)

> 한 줄 정리: 반으로 나누고 정렬하면서 합치는 정렬, 항상 O(n log n)이 보장된다

## 1. 언제 쓰는가
- 안정적인 O(n log n)이 필요할 때
- 연결 리스트 정렬 (퀵 정렬보다 유리)
- 외부 정렬 (데이터가 메모리에 다 안 들어올 때)
- 역전 쌍(inversion count) 문제

## 2. 핵심 아이디어
- 분할(Divide): 배열을 반으로 계속 나눔
- 정복(Conquer): 각 부분을 재귀적으로 정렬
- 병합(Merge): 정렬된 두 배열을 합침
- 항상 O(n log n) 보장, 단 추가 메모리 O(n) 필요

## 3. 시간/공간 복잡도
| 케이스 | 시간복잡도 |
|--------|-----------|
| 최선 | O(n log n) |
| 평균 | O(n log n) |
| 최악 | O(n log n) |
| 공간복잡도 | O(n) |

- stable 정렬 (같은 값의 순서 유지)

## 4. 기본 코드 (Python)

### 기본형
```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr

    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])

    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```

### 역전 쌍 카운트 (inversion count)
```python
count = 0

def merge_count(arr):
    global count
    if len(arr) <= 1:
        return arr

    mid = len(arr) // 2
    left = merge_count(arr[:mid])
    right = merge_count(arr[mid:])

    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
            count += len(left) - i  # 역전 쌍 카운트
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```

## 5. 이걸 떠올려야 할 때
- "최악도 O(n log n) 보장" → 병합 정렬
- "역전 쌍 개수 세기" → 병합 정렬 응용
- "stable 정렬 + O(n log n)" → 병합 정렬 (퀵 정렬은 unstable)
- 코테에서 직접 구현보다 `arr.sort()` 쓰되, 역전 쌍 문제에서 직접 구현

## 6. 자주 틀리는 포인트
- 슬라이싱(`arr[:mid]`)이 새 리스트를 만들어서 메모리 O(n) 추가 사용
- 재귀 깊이가 깊어지면 Python 스택 오버플로 가능 → `sys.setrecursionlimit` 설정
- 역전 쌍 카운트 시 `count += len(left) - i` 위치 헷갈리지 않기

## 7. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 수 정렬하기 2 | [백준 2751](https://www.acmicpc.net/problem/2751) | O(n log n) 정렬 |
| 반전 수 | [백준 1517](https://www.acmicpc.net/problem/1517) | 역전 쌍 카운트 |
