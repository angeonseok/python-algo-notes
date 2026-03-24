# Sprague-Grundy Theorem (스프라그-그런디 정리)

> 한 줄 정리: 모든 공정 게임은 님(Nim) 게임으로 환원할 수 있고, Grundy 값으로 승패를 판별한다

## 1. 언제 쓰는가
- 두 플레이어가 번갈아 움직이는 게임의 승패 판별
- 여러 게임이 동시에 진행될 때 전체 승패 판별
- 님(Nim) 게임 변형 문제

## 2. 핵심 개념

### 공정 게임 (Impartial Game)
```
- 두 플레이어가 동일한 행동을 할 수 있는 게임
- 마지막 수를 두는 플레이어가 이기는 규칙 (Normal play)
- 예: 님 게임, 돌 가져가기
```

### Grundy 값 (님버, Nimber)
```
- 각 게임 상태의 Grundy 값 = mex(후속 상태들의 Grundy 값)
- mex(minimum excludant): 집합에 포함되지 않는 최솟값 음이 아닌 정수
- Grundy 값이 0이면 → 현재 플레이어가 짐 (P-position)
- Grundy 값이 0이 아니면 → 현재 플레이어가 이김 (N-position)
```

### mex 예시
```
mex({0, 1, 2}) = 3
mex({0, 2})    = 1
mex({1, 2})    = 0
mex({})        = 0  (빈 집합)
```

### 스프라그-그런디 정리
```
여러 게임이 동시에 진행될 때:
전체 Grundy 값 = 각 게임의 Grundy 값의 XOR

전체 Grundy 값이 0 → 현재 플레이어가 짐
전체 Grundy 값이 0이 아님 → 현재 플레이어가 이김
```

## 3. 기본 코드 (Python)

### mex 계산
```python
def mex(s):
    # 집합 s에 포함되지 않는 최솟값 음이 아닌 정수
    i = 0
    while i in s:
        i += 1
    return i
```

### Grundy 값 계산 (메모이제이션)
```python
from functools import lru_cache

def grundy(state):
    # state: 현재 게임 상태
    # next_states(state): 이동 가능한 다음 상태들
    @lru_cache(maxsize=None)
    def _grundy(state):
        reachable = {_grundy(next_state) for next_state in next_states(state)}
        return mex(reachable)
    return _grundy(state)
```

### 돌 가져가기 게임
```python
from functools import lru_cache

# N개의 돌에서 1~K개를 가져갈 수 있을 때 Grundy 값
@lru_cache(maxsize=None)
def grundy_stones(n, k):
    if n == 0:
        return 0  # 가져갈 돌 없음 → 짐 (Grundy = 0)
    reachable = set()
    for take in range(1, min(k, n) + 1):
        reachable.add(grundy_stones(n - take, k))
    return mex(reachable)

# 예시: 10개 돌, 최대 3개 가져가기
for i in range(11):
    print(f"n={i}: grundy={grundy_stones(i, 3)}")
# 패턴: 0,1,2,3,0,1,2,3,... (주기 = k+1)
```

## 4. 실전 패턴

### 님 게임 (Nim)
```python
# N개의 더미, 각 더미에서 임의 개수 가져가기
# Grundy 값 = 더미 크기 자체
# 전체 Grundy = 모든 더미 크기의 XOR

def nim_winner(piles):
    xor = 0
    for pile in piles:
        xor ^= pile
    return xor != 0  # True면 현재 플레이어 승리
```

### 여러 게임 동시 진행
```python
# 게임 A와 게임 B를 동시에 진행
# 자기 턴에 둘 중 하나를 선택해서 이동

def combined_winner(state_a, state_b):
    g_a = grundy_a(state_a)
    g_b = grundy_b(state_b)
    return (g_a ^ g_b) != 0  # XOR이 0이 아니면 현재 플레이어 승리
```

### 보드 게임 Grundy 값 (2차원)
```python
@lru_cache(maxsize=None)
def grundy_2d(r, c):
    # (r, c)에서 이동 가능한 상태들의 Grundy 값
    reachable = set()
    for dr, dc in moves:  # 이동 방향
        nr, nc = r + dr, c + dc
        if 0 <= nr < R and 0 <= nc < C:
            reachable.add(grundy_2d(nr, nc))
    return mex(reachable)
```

## 5. 미제르 게임 (Misère)

### Normal play vs Misère
```
Normal play: 마지막 수를 두는 플레이어가 이김 (일반적)
Misère:      마지막 수를 두는 플레이어가 짐 (반대)
```

### 님 미제르 판별법
```
님 게임에서 Misère 규칙일 때:

모든 더미가 크기 1 이하이면:
    더미 개수가 홀수 → 현재 플레이어 짐
    더미 개수가 짝수 → 현재 플레이어 이김

그 외 (크기 2 이상인 더미가 하나라도 있으면):
    XOR이 0 → 현재 플레이어 짐
    XOR이 0이 아님 → 현재 플레이어 이김

즉, Normal play와 결론이 반대가 되는 경우는
"모든 더미가 크기 1일 때" 뿐
```

### 코드
```python
def nim_misere_winner(piles):
    xor = 0
    for pile in piles:
        xor ^= pile

    # 모든 더미가 크기 1 이하인지 확인
    all_one = all(p <= 1 for p in piles)

    if all_one:
        # 더미 개수(= 돌 개수)가 홀수면 현재 플레이어 짐
        return sum(piles) % 2 == 0  # True면 현재 플레이어 승리
    else:
        # Normal play와 동일
        return xor != 0

# 예시
print(nim_misere_winner([1, 1, 1]))  # False → 현재 플레이어 짐 (홀수 개)
print(nim_misere_winner([1, 2, 3]))  # Normal play와 동일하게 XOR = 0 → 짐
print(nim_misere_winner([2, 3]))     # XOR = 1 → 이김
```

### Normal play vs Misère 비교
| | Normal play | Misère |
|--|------------|--------|
| 승리 조건 | 마지막 수를 둠 | 마지막 수를 두지 않음 |
| 모든 더미 ≤ 1 | XOR로 판별 | XOR 반대 |
| 크기 2 이상 존재 | XOR로 판별 | XOR로 판별 (동일) |

## 6. 이걸 떠올려야 할 때
- "마지막 수를 두는 사람이 짐" → 미제르 → 님이면 all_one 여부로 분기
- "두 플레이어 게임, 누가 이기나" → 스프라그-그런디
- "두 플레이어 게임, 누가 이기나" → 스프라그-그런디
- "여러 게임 동시 진행" → 각 Grundy 값 XOR
- "님 게임 변형" → 더미 크기의 XOR
- Grundy 값에 주기가 있으면 → 큰 입력도 처리 가능

## 7. 자주 틀리는 포인트
- 마지막 수를 두는 사람이 지는 규칙(Misère)이면 별도 처리 필요
- mex는 집합에 없는 **최솟값** → 0부터 순서대로 확인
- 여러 게임 XOR 시 Grundy 값이 0인 게임도 포함해야 함
- 재귀가 깊어지면 `sys.setrecursionlimit` 필요

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 돌 게임 | [백준 9655](https://www.acmicpc.net/problem/9655) | 님 게임 기본 |
| 돌 게임 4 | [백준 9658](https://www.acmicpc.net/problem/9658) | 미제르 님 게임 |
| Nim | [백준 11959](https://www.acmicpc.net/problem/11959) | 님 게임 XOR |
| 돌 게임 5 | [백준 13007](https://www.acmicpc.net/problem/13007) | 스프라그-그런디 응용 |
