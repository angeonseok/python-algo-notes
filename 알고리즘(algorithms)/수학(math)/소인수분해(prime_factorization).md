# Prime Factorization (소인수분해)

> 한 줄 정리: 어떤 수를 소수들의 곱으로 분해한다, √N까지만 나눠보면 충분하다

## 1. 언제 쓰는가
- 약수 개수/약수의 합 구할 때
- 두 수의 GCD/LCM을 소인수로 분석할 때
- 특정 수가 어떤 소수로 이루어졌는지 확인
- 제곱수 판별

## 2. 핵심 아이디어
- 2부터 √N까지 나눠보며 소인수 추출
- √N까지 나눠도 안 떨어지면 나머지가 소수
- 각 소인수의 지수까지 함께 구하면 약수 개수 계산 가능

## 3. 시간복잡도
| | 복잡도 |
|--|--------|
| 시간 | O(√N) |

## 4. 기본 코드 (Python)

### 소인수 목록 구하기
```python
def prime_factors(n):
    factors = []
    d = 2
    while d * d <= n:
        while n % d == 0:
            factors.append(d)
            n //= d
        d += 1
    if n > 1:
        factors.append(n)  # 남은 수가 소수
    return factors

prime_factors(12)   # [2, 2, 3]
prime_factors(100)  # [2, 2, 5, 5]
```

### 소인수와 지수 함께 구하기
```python
def prime_factorization(n):
    factors = {}
    d = 2
    while d * d <= n:
        while n % d == 0:
            factors[d] = factors.get(d, 0) + 1
            n //= d
        d += 1
    if n > 1:
        factors[n] = factors.get(n, 0) + 1
    return factors

prime_factorization(12)   # {2: 2, 3: 1}  → 2² × 3
prime_factorization(100)  # {2: 2, 5: 2}  → 2² × 5²
```

### 약수 개수 구하기
```python
# 소인수분해 결과로 약수 개수 계산
# n = p1^a1 * p2^a2 * ... → 약수 개수 = (a1+1)(a2+1)...
def count_divisors(n):
    factors = prime_factorization(n)
    result = 1
    for exp in factors.values():
        result *= (exp + 1)
    return result

count_divisors(12)  # (2+1)(1+1) = 6 → 1,2,3,4,6,12
```

### 약수 전체 구하기
```python
def get_divisors(n):
    divisors = []
    for i in range(1, int(n**0.5) + 1):
        if n % i == 0:
            divisors.append(i)
            if i != n // i:
                divisors.append(n // i)
    return sorted(divisors)

get_divisors(12)  # [1, 2, 3, 4, 6, 12]
```

## 5. 이걸 떠올려야 할 때
- "약수 개수" → 소인수분해 후 지수 곱
- "약수 전체" → O(√N) 순회
- "소수인지 판별" → 소인수분해 후 인수가 자기 자신뿐이면 소수
- "제곱수 판별" → 모든 지수가 짝수면 제곱수

## 6. 자주 틀리는 포인트
- √N까지 나눈 후 `if n > 1` 체크 빠뜨리면 큰 소인수 누락
- 약수 개수 공식에서 지수에 +1 빠뜨리는 경우
- 약수 구할 때 `i == n // i`인 경우 (완전제곱수) 중복 추가 주의

## 7. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 소인수분해 | [백준 11653](https://www.acmicpc.net/problem/11653) | 소인수분해 기본 |
| 약수의 합 | [백준 17427](https://www.acmicpc.net/problem/17427) | 약수 응용 |
| 제곱수 판별 | [백준 1025](https://www.acmicpc.net/problem/1025) | 소인수분해 응용 |
