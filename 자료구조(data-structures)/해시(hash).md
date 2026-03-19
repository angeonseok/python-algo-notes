# Hash (해시 - set / dict)

> 한 줄 정리: 키를 해시 함수로 변환해 O(1)에 저장/탐색/삭제하는 구조, Python에서는 set과 dict가 해시 테이블로 구현됨

## 1. 언제 쓰는가
- 빠른 탐색/삽입/삭제가 필요할 때 (O(1))
- 중복 제거
- 방문 여부 체크 (visited)
- 값의 등장 횟수 카운트
- 두 집합의 교집합/합집합/차집합

## 2. 핵심 아이디어
- 해시 함수: 키 → 인덱스로 변환
- 같은 키는 항상 같은 인덱스 → O(1) 접근
- 충돌(collision) 발생 가능 → 최악의 경우 O(n)이지만 평균 O(1)
- `set` = 키만 저장, `dict` = 키-값 쌍 저장

## 3. 시간복잡도
| 연산 | 평균 | 최악 |
|------|------|------|
| 삽입 | O(1) | O(n) |
| 탐색 | O(1) | O(n) |
| 삭제 | O(1) | O(n) |

- 최악은 해시 충돌이 심할 때, 실제로는 거의 O(1)

## 4. 기본 코드 (Python)

### set
```python
s = set()

# 삽입
s.add(1)
s.add(2)
s.add(2)  # 중복 무시

# 탐색
if 1 in s:      # O(1)
    print("있음")

# 삭제
s.remove(1)     # 없으면 KeyError
s.discard(1)    # 없어도 에러 없음

# 집합 연산
a = {1, 2, 3}
b = {2, 3, 4}
a | b   # 합집합 {1, 2, 3, 4}
a & b   # 교집합 {2, 3}
a - b   # 차집합 {1}
a ^ b   # 대칭 차집합 {1, 4}

# 초기화
s = set([1, 2, 3])      # 리스트 → set (중복 제거)
s = {1, 2, 3}           # 리터럴
```

### dict
```python
d = {}

# 삽입 / 수정
d['a'] = 1
d['b'] = 2

# 탐색
if 'a' in d:        # O(1)
    print(d['a'])

d.get('a')          # 없으면 None 반환 (KeyError 없음)
d.get('z', 0)       # 없으면 기본값 0 반환

# 삭제
del d['a']          # 없으면 KeyError
d.pop('a', None)    # 없어도 에러 없음

# 순회
for k in d:             # 키 순회
    print(k, d[k])
for k, v in d.items():  # 키-값 순회
    print(k, v)

# 등장 횟수 카운트
from collections import Counter
arr = [1, 2, 2, 3, 3, 3]
cnt = Counter(arr)  # Counter({3: 3, 2: 2, 1: 1})
cnt[3]              # 3
cnt.most_common(2)  # [(3, 3), (2, 2)]

# 기본값 설정
from collections import defaultdict
d = defaultdict(int)    # 없는 키 접근 시 0
d = defaultdict(list)   # 없는 키 접근 시 []
d['a'] += 1             # KeyError 없음
```

## 5. 실전 패턴

### 중복 제거
```python
arr = [1, 2, 2, 3, 3, 3]
unique = list(set(arr))  # 순서 보장 안 됨
```

### 방문 체크 (visited)
```python
visited = set()
if node not in visited:
    visited.add(node)
```

### 두 리스트 공통 원소
```python
a = [1, 2, 3, 4]
b = [3, 4, 5, 6]
common = set(a) & set(b)  # {3, 4}
```

### 등장 횟수 카운트
```python
from collections import Counter
s = "abracadabra"
cnt = Counter(s)
cnt['a']  # 5
```

### anagram 판별
```python
def is_anagram(s, t):
    return Counter(s) == Counter(t)
```

## 6. 이걸 떠올려야 할 때
- "탐색을 자주 해야 한다" → list 대신 set/dict (O(n) → O(1))
- "중복을 없애야 한다" → set
- "등장 횟수가 필요하다" → Counter 또는 defaultdict
- "그래프/BFS에서 방문 체크" → set이 list보다 빠름
- "없는 키도 안전하게 접근" → defaultdict 또는 get()

## 7. 자주 틀리는 포인트
- `set`은 순서 보장 안 됨 → 순서 필요하면 list 써야 함
- `dict`는 Python 3.7부터 삽입 순서 보장
- `remove()`는 없으면 KeyError → 불확실하면 `discard()` 쓰기
- `list`로 `in` 탐색하면 O(n) → 자주 탐색하면 `set`으로 변환
- `defaultdict`는 없는 키 접근 시 자동 생성 → 의도치 않게 키가 생길 수 있음

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 나는야 포켓몬 마스터 | [백준 1620](https://www.acmicpc.net/problem/1620) | dict 기본 |
| 숫자 카드 2 | [백준 10816](https://www.acmicpc.net/problem/10816) | Counter 활용 |
| 두 수의 합 | [리트코드 1](https://leetcode.com/problems/two-sum/) | dict로 O(n) 풀기 |
| 아나그램 | [백준 6064](https://www.acmicpc.net/problem/6064) | set/Counter 활용 |
