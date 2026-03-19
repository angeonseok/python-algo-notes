# Array (배열)

> 한 줄 정리: 연속된 메모리에 데이터를 순서대로 저장하는 구조, Python에서는 list가 배열 역할

## 1. 언제 쓰는가
- 인덱스로 빠르게 접근해야 할 때
- 데이터를 순서대로 저장할 때
- 슬라이싱, 정렬이 필요할 때
- 2차원 배열로 그리드/행렬 표현할 때

## 2. 핵심 아이디어
- Python `list`는 dynamic array → 크기 자동 조절
- 인덱스로 O(1) 접근 가능
- 중간 삽입/삭제는 O(n) → 느림
- 음수 인덱스 지원: `arr[-1]` = 마지막 원소

## 3. 시간복잡도
| 연산 | 복잡도 |
|------|--------|
| 접근 (arr[i]) | O(1) |
| 탐색 | O(n) |
| 맨 뒤 삽입 (append) | O(1) |
| 중간 삽입 (insert) | O(n) |
| 맨 뒤 삭제 (pop) | O(1) |
| 중간 삭제 (pop(i)) | O(n) |
| 슬라이싱 (arr[i:j]) | O(j-i) |

## 4. 기본 코드 (Python)
```python
arr = [1, 2, 3, 4, 5]

# 접근
arr[0]   # 1
arr[-1]  # 5

# 슬라이싱
arr[1:3]   # [2, 3]
arr[::-1]  # [5, 4, 3, 2, 1] 뒤집기

# 삽입 / 삭제
arr.append(6)       # 맨 뒤 추가 O(1)
arr.insert(2, 99)   # 중간 삽입 O(n)
arr.pop()           # 맨 뒤 삭제 O(1)
arr.pop(2)          # 중간 삭제 O(n)

# 탐색
if 3 in arr:        # O(n)
    print("있음")
arr.index(3)        # 인덱스 반환, 없으면 ValueError

# 정렬
arr.sort()              # 제자리 정렬
sorted_arr = sorted(arr)  # 새 리스트 반환
arr.sort(reverse=True)  # 내림차순
```

### 2차원 배열
```python
# 초기화
N, M = 3, 4
grid = [[0] * M for _ in range(N)]  # 3x4 배열

# 주의: 아래처럼 하면 안 됨 (같은 리스트를 참조)
grid = [[0] * M] * N  # 절대 쓰지 말 것

# 접근
grid[1][2] = 5
print(grid[1][2])

# 순회
for i in range(N):
    for j in range(M):
        print(grid[i][j])
```

## 5. 실전 패턴

### 투 포인터
```python
# 정렬된 배열에서 합이 target인 쌍 찾기
def two_sum(arr, target):
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

### 슬라이딩 윈도우
```python
# 길이 k인 구간의 최대 합
def max_sum(arr, k):
    window = sum(arr[:k])
    result = window
    for i in range(k, len(arr)):
        window += arr[i] - arr[i - k]
        result = max(result, window)
    return result
```

### 누적 합 (Prefix Sum)
```python
# 구간 합을 O(1)에 구하기
arr = [1, 2, 3, 4, 5]
prefix = [0] * (len(arr) + 1)
for i in range(len(arr)):
    prefix[i + 1] = prefix[i] + arr[i]

# arr[l:r+1] 구간 합
def range_sum(l, r):
    return prefix[r + 1] - prefix[l]
```

## 6. 이걸 떠올려야 할 때
- "연속된 구간의 합/최솟값/최댓값" → 슬라이딩 윈도우 or 누적 합
- "정렬된 배열에서 두 값의 조건" → 투 포인터
- "2차원 격자/지도" → 2차원 배열
- "인덱스로 빠르게 접근" → 배열

## 7. 자주 틀리는 포인트
- `[[0] * M] * N` 쓰면 행이 모두 같은 객체를 참조 → 반드시 리스트 컴프리헨션 사용
- `arr.pop(0)`은 O(n) → 앞에서 자주 꺼내야 하면 `deque` 쓰기
- 슬라이싱은 새 리스트를 반환 → 원본 안 바뀜
- `in` 연산자는 O(n) → 자주 탐색하면 `set`으로 변환

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 구간 합 구하기 | [백준 11659](https://www.acmicpc.net/problem/11659) | 누적 합 기본 |
| 투 포인터 | [백준 2003](https://www.acmicpc.net/problem/2003) | 투 포인터 입문 |
| 나머지 합 | [백준 10986](https://www.acmicpc.net/problem/10986) | 누적 합 심화 |
| 컨베이어 벨트 위의 로봇 | [백준 20055](https://www.acmicpc.net/problem/20055) | 2차원 배열 시뮬레이션 |
