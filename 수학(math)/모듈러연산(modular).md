# Modular Arithmetic (모듈러 연산)

> 한 줄 정리: 큰 수를 다룰 때 나머지 연산으로 오버플로를 방지하고, 모듈러 역원으로 나눗셈도 처리한다

## 1. 언제 쓰는가
- 답이 매우 커서 "MOD로 나눈 나머지"를 출력할 때
- 조합(nCr) 계산
- 거듭제곱의 나머지

## 2. 기본 모듈러 연산
```python
MOD = 1_000_000_007

(a + b) % MOD           # 덧셈
(a - b + MOD) % MOD     # 뺄셈 (음수 방지)
(a * b) % MOD           # 곱셈
pow(a, b, MOD)          # 거듭제곱 a^b % MOD, O(log b)
```

## 3. 모듈러 역원과 페르마 소정리

### 왜 나눗셈은 그냥 % 하면 안 되나?
```
일반 나눗셈: 10 / 2 = 5
모듈러에서: (10 % 7) / (2 % 7) = 3 / 2 → 정수 나눗셈 불가

즉, (a / b) % M ≠ (a % M) / (b % M)
→ 나눗셈은 "b의 모듈러 역원"을 곱하는 방식으로 처리해야 함
```

### 모듈러 역원이란?
```
일반 수에서:  a * (1/a) = 1
모듈러에서:  b * b^(-1) ≡ 1 (mod M)

여기서 b^(-1)이 b의 모듈러 역원

즉, (a / b) % M = (a * b^(-1)) % M 으로 나눗셈을 곱셈으로 바꿈
```

### 페르마 소정리
```
M이 소수이고 b가 M의 배수가 아닐 때:

    b^(M-1) ≡ 1 (mod M)

양변을 b로 나누면:

    b^(M-2) ≡ b^(-1) (mod M)

→ b의 모듈러 역원 = b^(M-2) % M
→ pow(b, M-2, MOD) 로 O(log M)에 계산 가능!
```

### 코드
```python
MOD = 1_000_000_007  # 소수여야 함

def mod_inverse(b):
    return pow(b, MOD - 2, MOD)

# a / b % MOD
result = a * mod_inverse(b) % MOD

# 예시: 10 / 2 % MOD
print(10 * mod_inverse(2) % MOD)  # 5
```

## 4. 조합(nCr) % MOD 빠르게 계산하기

### 왜 어려운가?
```
nCr = n! / (r! * (n-r)!)

팩토리얼이 엄청나게 크기 때문에 그냥 계산하면 오버플로
→ 매 단계마다 % MOD 해야 함
→ 근데 나눗셈이 있어서 그냥 % 불가 → 모듈러 역원 필요
```

### 단계별 풀이
```
nCr % MOD
= n! / (r! * (n-r)!) % MOD
= n! * (r!)^(-1) * ((n-r)!)^(-1) % MOD
= factorial[n] * inv_factorial[r] * inv_factorial[n-r] % MOD

여기서:
- factorial[i]     = i! % MOD              → 전처리로 미리 계산
- inv_factorial[i] = (i!)^(-1) % MOD       → 전처리로 미리 계산
```

### 전처리 코드 (전체)
```python
MOD = 1_000_000_007
MAX = 200_001  # 문제 최대 N보다 크게

# 1. 팩토리얼 전처리 O(N)
factorial = [1] * MAX
for i in range(1, MAX):
    factorial[i] = factorial[i-1] * i % MOD

# 2. 역팩토리얼 전처리 O(N + log MOD)
inv_factorial = [1] * MAX
inv_factorial[MAX-1] = pow(factorial[MAX-1], MOD-2, MOD)  # 마지막 값만 pow
for i in range(MAX-2, -1, -1):                             # 나머지는 역방향으로 O(1)씩
    inv_factorial[i] = inv_factorial[i+1] * (i+1) % MOD

# 3. 조합 계산 O(1)
def comb(n, r):
    if r < 0 or r > n:
        return 0
    return factorial[n] * inv_factorial[r] % MOD * inv_factorial[n-r] % MOD
```

### 왜 역팩토리얼을 역방향으로 채우나?
```
pow()를 매번 쓰면 O(N log MOD) → 느림

더 빠른 방법:
i! = (i-1)! * i
→ (i!)^(-1) = ((i-1)!)^(-1) * i^(-1)
→ inv_factorial[i-1] = inv_factorial[i] * i % MOD

즉, 마지막 값만 pow()로 구하고
나머지는 역방향으로 O(1)씩 채울 수 있음
→ 전체 O(N)으로 전처리 완료!
```

### 흐름 정리
```
① factorial 배열 앞에서 뒤로 채움        O(N)
② inv_factorial[MAX-1] 만 pow()로 계산   O(log MOD)
③ inv_factorial 뒤에서 앞으로 채움       O(N)
④ comb(n, r) 호출                        O(1)
```

### 사용 예시
```python
comb(10, 3)    # 120
comb(100, 50)  # 매우 큰 수 % MOD
comb(5, 0)     # 1
comb(5, 6)     # 0 (예외처리)
```

## 5. 이걸 떠올려야 할 때
- "답을 MOD로 나눈 나머지" → 매 연산마다 MOD 적용
- "nCr % MOD" → 팩토리얼 전처리 + 역팩토리얼
- "거듭제곱 % MOD" → `pow(a, b, MOD)`
- nCr 쿼리가 많으면 → 전처리 필수, 단발성이면 그냥 pow로 역원 구하기

## 6. 자주 틀리는 포인트
- 뺄셈에서 음수 나올 수 있음 → `(a - b + MOD) % MOD`
- MOD가 소수가 아니면 페르마 소정리 사용 불가
- 역팩토리얼 전처리 시 MAX 크기를 문제 N보다 크게 잡아야 함
- `comb(n, r)`에서 r < 0 또는 r > n 예외처리 빠뜨리는 경우
- `pow(a, b, MOD)`는 Python 내장이라 직접 구현 불필요

## 7. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 이항 계수 3 | [백준 11401](https://www.acmicpc.net/problem/11401) | 모듈러 역원 + 조합 |
| 조합 0의 개수 | [백준 2004](https://www.acmicpc.net/problem/2004) | 소인수분해 응용 |
| 다리 놓기 | [백준 1010](https://www.acmicpc.net/problem/1010) | 조합 기본 |
