# Deque (덱)

> 한 줄 정리: 앞뒤 양쪽에서 삽입/삭제가 모두 가능한 구조

## 1. 언제 쓰는가
- 앞뒤 양쪽에서 삽입/삭제가 필요할 때
- 슬라이딩 윈도우 최솟값/최댓값 문제
- 팰린드롬 검사
- 스택과 큐를 동시에 써야 할 때
- 앞에서도 빠르게 꺼내야 할 때 (list 대체)

## 2. 핵심 아이디어
- 양방향으로 열려있는 큐
- appendleft / popleft → 앞쪽 O(1)
- append / pop → 뒤쪽 O(1)
- Python `collections.deque`가 곧 덱

## 3. 시간복잡도
- append / appendleft: O(1)
- pop / popleft: O(1)
- 인덱스 접근 (deque[i]): O(n) → 자주 쓰면 list 고려

## 4. 기본 코드 (Python)
```python
from collections import deque

dq = deque()

# 뒤에 삽입 / 앞에 삽입
dq.append(1)
dq.appendleft(0)

# 뒤에서 제거 / 앞에서 제거
dq.pop()
dq.popleft()

# 앞 / 뒤 확인
dq[0]   # front
dq[-1]  # back

# 초기값으로 생성
dq = deque([1, 2, 3])

# maxlen 설정 (꽉 차면 반대쪽이 자동으로 빠짐)
dq = deque(maxlen=3)
```

## 5. 실전 패턴

### 슬라이딩 윈도우 최솟값 (단조 덱)
- 덱에 인덱스를 저장, 앞쪽은 항상 현재 윈도우의 최솟값
```python
from collections import deque

def sliding_window_min(arr, k):
    dq = deque()  # 인덱스 저장
    result = []

    for i, v in enumerate(arr):
        # 윈도우 벗어난 인덱스 앞에서 제거
        while dq and dq[0] < i - k + 1:
            dq.popleft()

        # 현재 값보다 큰 값은 뒤에서 제거 (단조 증가 유지)
        while dq and arr[dq[-1]] > v:
            dq.pop()

        dq.append(i)

        if i >= k - 1:
            result.append(arr[dq[0]])

    return result
```

### 팰린드롬 검사
```python
from collections import deque

def is_palindrome(s):
    dq = deque(s)
    while len(dq) > 1:
        if dq.popleft() != dq.pop():
            return False
    return True
```

### 회전 (rotate)
```python
dq = deque([1, 2, 3, 4, 5])
dq.rotate(2)   # 오른쪽으로 2칸 → [4, 5, 1, 2, 3]
dq.rotate(-2)  # 왼쪽으로 2칸 → [1, 2, 3, 4, 5]
```

## 6. 이걸 떠올려야 할 때
- "구간 최솟값/최댓값을 빠르게 구하라" → 단조 덱
- "앞뒤로 뭔가 비교해야 한다" → 덱
- "큐인데 앞에서도 넣어야 한다" → 덱
- "회전하는 배열/원형 구조" → deque.rotate()

## 7. 자주 틀리는 포인트
- 덱은 인덱스 접근이 O(n) → 중간 원소 자주 접근하면 list가 나음
- 슬라이딩 윈도우에서 값이 아닌 인덱스를 저장해야 윈도우 범위 체크 가능
- `rotate()`는 양수면 오른쪽, 음수면 왼쪽 → 헷갈리면 직접 테스트
- maxlen 설정하면 반대쪽이 자동으로 밀려남 → 의도치 않게 데이터 날릴 수 있음

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 덱 | [백준 10866](https://www.acmicpc.net/problem/10866) | 덱 기본 구현 |
| 요세푸스 문제 | [백준 1158](https://www.acmicpc.net/problem/1158) | rotate 활용 |
| 슬라이딩 윈도우 최솟값 | [백준 11003](https://www.acmicpc.net/problem/11003) | 단조 덱 |
| 카드2 | [백준 2164](https://www.acmicpc.net/problem/2164) | 덱으로 시뮬레이션 |
