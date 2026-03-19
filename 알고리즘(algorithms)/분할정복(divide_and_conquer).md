# Divide and Conquer (분할 정복)

> 한 줄 정리: 문제를 작은 부분으로 나누고 각각 해결한 뒤 합치는 방법

## 1. 언제 쓰는가
- 문제를 동일한 구조의 더 작은 문제로 나눌 수 있을 때
- 부분 문제의 결과를 합쳐서 전체 문제를 풀 수 있을 때
- 큰 입력을 반씩 줄여나갈 수 있을 때
- 거듭제곱, 행렬 곱, 최근접 점 쌍 등

## 2. 핵심 아이디어
- 분할(Divide): 문제를 더 작은 부분으로 나눔
- 정복(Conquer): 각 부분을 재귀적으로 해결
- 합치기(Combine): 부분 결과를 합쳐 전체 해답 도출
- 매 단계에서 절반씩 줄어들면 → O(log N) or O(N log N)

## 3. 시간복잡도
| 문제 | 복잡도 |
|------|--------|
| 이진 탐색 | O(log N) |
| 병합 정렬 | O(N log N) |
| 거듭제곱 | O(log N) |
| 행렬 거듭제곱 | O(M³ log N) |

- 마스터 정리: T(N) = aT(N/b) + f(N) 형태로 복잡도 분석 가능

## 4. 기본 코드 (Python)

### 분할 정복 기본 구조
```python
def divide_and_conquer(problem):
    # 기저 조건
    if 문제가_충분히_작으면:
        return 직접_해결(problem)

    # 분할
    left, right = split(problem)

    # 정복
    left_result  = divide_and_conquer(left)
    right_result = divide_and_conquer(right)

    # 합치기
    return combine(left_result, right_result)
```

### 거듭제곱 (빠른 지수)
```python
def power(base, exp, mod=None):
    if exp == 0:
        return 1
    if exp % 2 == 0:
        half = power(base, exp // 2, mod)
        result = half * half
    else:
        result = base * power(base, exp - 1, mod)

    return result % mod if mod else result

# 사용
power(2, 10)        # 1024
power(2, 10, 1000)  # 24 (mod 1000)
```

### 행렬 거듭제곱 (피보나치 O(log N))
```python
def mat_mul(A, B, mod):
    n = len(A)
    C = [[0] * n for _ in range(n)]
    for i in range(n):
        for j in range(n):
            for k in range(n):
                C[i][j] = (C[i][j] + A[i][k] * B[k][j]) % mod
    return C

def mat_pow(M, exp, mod):
    n = len(M)
    result = [[1 if i == j else 0 for j in range(n)] for i in range(n)]  # 단위 행렬
    while exp:
        if exp % 2:
            result = mat_mul(result, M, mod)
        M = mat_mul(M, M, mod)
        exp //= 2
    return result

# 피보나치 N번째 O(log N)
def fib(n, mod=1000000007):
    if n <= 1:
        return n
    M = [[1, 1], [1, 0]]
    result = mat_pow(M, n - 1, mod)
    return result[0][0]
```

### 쿼드 트리 / 색종이 분할
```python
def quad(grid, r, c, size):
    color = grid[r][c]
    # 모두 같은 색이면 압축
    for i in range(r, r + size):
        for j in range(c, c + size):
            if grid[i][j] != color:
                half = size // 2
                quad(grid, r,        c,        half)
                quad(grid, r,        c + half, half)
                quad(grid, r + half, c,        half)
                quad(grid, r + half, c + half, half)
                return
    # 전부 같은 색
    result.append(color)
```

## 5. 이걸 떠올려야 할 때
- "N이 매우 크고 거듭제곱/반복이 필요" → 빠른 지수
- "피보나치를 매우 큰 N까지" → 행렬 거듭제곱
- "격자를 4등분해서 처리" → 쿼드 트리
- "절반씩 나눠서 각각 풀고 합칠 수 있다" → 분할 정복

## 6. 자주 틀리는 포인트
- 기저 조건 빠뜨리면 무한 재귀
- 거듭제곱에서 mod 연산 빠뜨리면 오버플로 → 중간마다 mod 적용
- 행렬 거듭제곱에서 단위 행렬 초기화 빠뜨리는 경우
- 쿼드 트리에서 범위 계산 실수 → r, c, size 꼼꼼히 체크

## 7. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 색종이 만들기 | [백준 2630](https://www.acmicpc.net/problem/2630) | 쿼드 트리 기본 |
| 쿼드 트리 | [백준 1992](https://www.acmicpc.net/problem/1992) | 분할 정복 기본 |
| 거듭제곱 | [백준 1629](https://www.acmicpc.net/problem/1629) | 빠른 지수 |
| 피보나치 수 6 | [백준 11444](https://www.acmicpc.net/problem/11444) | 행렬 거듭제곱 |
