# Trie (트라이)

> 한 줄 정리: 문자열을 트리 구조로 저장해 O(L)에 탐색/삽입하는 자료구조 (L = 문자열 길이)

## 1. 언제 쓰는가
- 문자열 집합에서 특정 문자열 탐색
- 접두사(prefix) 검색
- 자동완성
- 문자열 정렬

## 2. 핵심 아이디어
- 각 노드가 문자 하나를 나타냄
- 루트에서 리프까지의 경로가 하나의 문자열
- 공통 접두사를 공유 → 메모리 효율적
- 탐색/삽입 모두 O(L)

## 3. 시간/공간 복잡도
| 연산 | 복잡도 |
|------|--------|
| 삽입 | O(L) |
| 탐색 | O(L) |
| 공간 | O(알파벳 수 × 노드 수) |

## 4. 기본 코드 (Python)

### 딕셔너리 기반 구현 (간단)
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False  # 단어의 끝 여부

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for c in word:
            if c not in node.children:
                node.children[c] = TrieNode()
            node = node.children[c]
        node.is_end = True

    def search(self, word):
        node = self.root
        for c in word:
            if c not in node.children:
                return False
            node = node.children[c]
        return node.is_end

    def starts_with(self, prefix):
        # prefix로 시작하는 단어가 있는지
        node = self.root
        for c in prefix:
            if c not in node.children:
                return False
            node = node.children[c]
        return True

# 사용
trie = Trie()
trie.insert("apple")
trie.insert("app")
trie.insert("banana")

trie.search("apple")      # True
trie.search("app")        # True
trie.search("ap")         # False (끝이 아님)
trie.starts_with("app")   # True
trie.starts_with("ban")   # True
trie.starts_with("cat")   # False
```

### 배열 기반 구현 (빠름, 알파벳 소문자 전용)
```python
class Trie:
    def __init__(self):
        self.children = [None] * 26
        self.is_end = False

    def insert(self, word):
        node = self
        for c in word:
            idx = ord(c) - ord('a')
            if not node.children[idx]:
                node.children[idx] = Trie()
            node = node.children[idx]
        node.is_end = True

    def search(self, word):
        node = self
        for c in word:
            idx = ord(c) - ord('a')
            if not node.children[idx]:
                return False
            node = node.children[idx]
        return node.is_end
```

## 5. 실전 패턴

### 접두사 카운트
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.count = 0      # 이 노드를 지나는 단어 수
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for c in word:
            if c not in node.children:
                node.children[c] = TrieNode()
            node = node.children[c]
            node.count += 1
        node.is_end = True

    def count_prefix(self, prefix):
        node = self.root
        for c in prefix:
            if c not in node.children:
                return 0
            node = node.children[c]
        return node.count
```

### XOR 최댓값 (비트 트라이)
```python
class BitTrie:
    def __init__(self):
        self.children = [None, None]

    def insert(self, num, bit=29):
        node = self
        for i in range(bit, -1, -1):
            b = (num >> i) & 1
            if not node.children[b]:
                node.children[b] = BitTrie()
            node = node.children[b]

    def max_xor(self, num, bit=29):
        node = self
        result = 0
        for i in range(bit, -1, -1):
            b = (num >> i) & 1
            want = 1 - b  # XOR 최대화를 위해 반대 비트 선택
            if node.children[want]:
                result |= (1 << i)
                node = node.children[want]
            else:
                node = node.children[b]
        return result
```

## 6. 이걸 떠올려야 할 때
- "문자열 집합에서 접두사 검색" → 트라이
- "자동완성" → 트라이
- "XOR 최댓값" → 비트 트라이
- 단순 문자열 탐색만이면 → set이 더 간단

## 7. 자주 틀리는 포인트
- `is_end` 체크 빠뜨리면 접두사도 단어로 인식
- 배열 기반에서 소문자 외 문자 들어오면 인덱스 오류
- 메모리 많이 씀 → 노드 수 × 알파벳 수만큼 필요

## 8. 관련 문제
| 문제 | 링크 | 메모 |
|------|------|------|
| 트라이 | [백준 5052](https://www.acmicpc.net/problem/5052) | 트라이 기본 |
| 문자열 집합 | [백준 14425](https://www.acmicpc.net/problem/14425) | 트라이 or set |
| XOR 최댓값 | [백준 13505](https://www.acmicpc.net/problem/13505) | 비트 트라이 |
