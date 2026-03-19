# Stack (스택)

> 한 줄 정리: 가장 최근에 넣은 것을 먼저 꺼내는 구조 (LIFO)

## 1. 언제 쓰는가
- LIFO(Last In First Out) 구조가 필요할 때
- 가장 최근에 넣은 값을 먼저 처리해야 할 때
- 괄호 검사 문제
- 문자열 처리 (뒤집기, 폭발 문자열)
- DFS를 재귀 대신 구현할 때
- 상태를 저장했다가 되돌리는 경우 (undo)

## 2. 핵심 아이디어
- 가장 마지막에 넣은 데이터를 먼저 꺼낸다.
- push → 삽입 (`append`)
- pop → 제거 (`pop`)
- top → 마지막 원소 (`stack[-1]`)
- Python에서는 list로 구현 가능

## 3. 시간복잡도
- push: O(1)
- pop: O(1)
- top: O(1)

## 4. 기본 코드 (Python)
```python
stack = []

# push
stack.append(1)
stack.append(2)

# pop
top = stack.pop()

# top 확인
top = stack[-1]

# empty 체크
if not stack:
    print("empty")
```

## 5. 실전 패턴

### 괄호 검사
```python
stack = []
for c in s:
    if c == '(':
        stack.append(c)
    elif c == ')':
        if not stack:
            return False
        stack.pop()
return not stack
```

### 단조 스택 (Monotonic Stack)
- 현재 원소보다 크거나 작은 이전 원소를 빠르게 찾을 때
- "오큰수", "히스토그램" 유형에서 자주 등장
```python
stack = []
for i, v in enumerate(arr):
    while stack and arr[stack[-1]] < v:
        idx = stack.pop()
        # arr[idx]의 오른쪽 첫 번째 큰 수 = v
    stack.append(i)
```

### DFS (재귀 대신 스택)
```python
stack = [start]
visited = set()
while stack:
    node = stack.pop()
    if node in visited:
        continue
    visited.add(node)
    for neighbor in graph[node]:
        stack.append(neighbor)
```

## 6. 이걸 떠올려야 할 때
- "이전 값과 비교해야 한다" → 스택에 이전 값 쌓아두기
- "뒤에서부터 처리해야 한다" → 스택으로 순서 뒤집기
- "열고 닫는 쌍이 있다" → 괄호, 태그 검사
- "가장 가까운 ~를 찾아라" → 단조 스택
- "중간에 뭔가 터지거나 사라진다" → 폭발 문자열 유형

## 7. 자주 틀리는 포인트
- pop() 전에 empty 체크 안 해서 IndexError 나는 경우
- top을 확인만 하고 싶은데 실수로 pop() 해버리는 경우
- `len(stack) > 0` 대신 `if stack` 으로 쓰는 게 파이썬스러움
- 단조 스택에서 인덱스를 넣을지 값을 넣을지 헷갈리는 경우 (보통 인덱스를 넣는 게 유연함)

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 올바른 괄호 | [백준 4949](https://www.acmicpc.net/problem/4949) | 스택 기본 |
| 오큰수 | [백준 17298](https://www.acmicpc.net/problem/17298) | 단조 스택 입문 |
| 폭발 문자열 | [백준 9935](https://www.acmicpc.net/problem/9935) | 문자열 + 스택 |
| 히스토그램 | [백준 1725](https://www.acmicpc.net/problem/1725) | 단조 스택 심화 |
