---
title: "기초 알고리즘과 파이썬 코딩"
categories: python algorithm
---
[토크ON강좌](https://www.youtube.com/playlist?list=PL9mhQYIlKEhfg0aLdaO04wYUovLMXY4DU)
를 참고하여 필기한 내용.

## 문제 해결(Problem Solving)

- 구현
- 효율성
- 절차적 사고
- 디버깅

> 4가지 역량을 키우자!

> 같은 유형의 문제라도 여러 번... 많이 풀어봐야...

## 파인만 알고리즘

1. Write down the problem
2. Think very hard
3. Write down the answer

## Time Complexity

- 시간 제한, 메모리 제한 꼭 확인
- 연산 적게, 입력 적게
- 입력 n -> 연산 2n

## Big-O Notation
---> 점근적 표기법
O(n!)>O(2^N)>O(N^2)>O(NlogN)>O(N)>O(√N)>O(logN)>O(1)

## 수, 문자, 배열

- 숫자 -> 형변환 int(input())
- 문자단위 (char)

```python
char_lst = list(input())
lst = list(map(int, input().split()))
```

- map(x, y)는 x 함수를 y 원소에 모두 적용한 map 객체를 return.
- 나중에 char_lst 내용 -> 문자열: join 메서드
- **문제에서는 약간의 여백을 두는 게 좋음.** *EX. 100이면 배열 -> 105로*
cf. 표기법
  - 함수: camelCase
  - 변수: snake_case
  - PascalCase

## 코드 예제

> return 방식

```python
# 1번
def function(x):
    if x:
        return True
    else:
        return False
```

```python
# 2번
def function(x):
    if x:   return True
    return False
```

```python
# 3번
def function(x):
    return True if x else False 
```

=> 2, 3번이 나음.

> 문자열 타입을 정수 타입으로 변환

```python
def stoi(s, n):
    ret = 0
    l = len(s)
    for i in range(l):
        ret += int(s[i])*n**(l-i-1)
    return ret
```

```python
def stoi(s, n):
    ret = 0
    for i in s:
        ret = ret*n + int(i)
    return ret
```

> 제곱

```python
def pow_custom(a, b):
    ret = 1
    while b:
        if b%2==1:  ret *= a
        a = a*a
        b //= 2
    return ret
```

```python
def pow_custom(a, b, mod):
    ret = 1
    while b:
        if b % 2 == 1:  ret = ret*a%mod
        a = a*a%mod
        b //= 2
    return ret
```

> 최대공약수

```python
# 내가 짜본 코드
def gcd(a, b):
    r = a % b
    while r!=0:
        a = b
        b = r
        r = a%b
        if r==0:
            return b
```

```python
# 1부터 체크하는 방식
def gcd(a, b):
    ret = 0
    for i in range(min(a, b)):
        if a%i==0 and b%i==0:
            ret = i
    return ret
```

```python
# min(a, b)부터 체크하는 방식
# 훨씬 빠르다!
def gcd(a, b):
    for i in range(min(a, b), 0, -1):
        if a%i==0 and b%i==0:
            return i
```

```python
# 유클리드 호제법
def gcd(a, b):
    return b if a%b==0 else gcd(b, a%b)
```

> 소수 판별법

```python
# O(N)
def isPrime(N):
    for i in range(2, N):
        if N%i==0:  return False
    return True
```

```python
# O(√N)
def ifPrime(N):
    i = 2
    while i*i<=N:
        if N%i==0:  return False
        i += 1
    return True
```

```python
# 에라토스테네스의 체
def era(N):
    ck, p = [False for _ in range(N+1)], []
    for i in range(2, N+1):
        if ck[i]: continue
        p.append(i)
        for j in range(i*i, N+1, i):
            ck[j] = True
    return ck, p
```

> 하노이 탑 예제(대표적인 재귀함수 예제)

```python
def hanoi(st, ed, sz):
    if sz==1:   return print(st, ed)
    hanoi(st, 6-st-ed, sz-1)
    print(st, ed)
    hanoi(6-st-ed, ed, sz-1)

n = int(input())
print(2**n-1)
hanoi(1, 3, n)
```

=> 최저조건에 대한 조건문!

## 정렬 알고리즘

- selection, bubble sort -> O(N^2)
- quick sort -> O(NlogN), unstable
- merge sort -> O(NlogN), stable
- radix sort: 범위가 짧을 때 쓰기 좋음

## 선형 자료구조

- stack: LIFO
- queue: FIFO
- deque: rotate 가능. 2개 포인터 사용. 큐+스택

* list 출력 시: print(ans)

```python
print(f"<{str(ans)[1:-1]}>")
```

* cf. fstring

```python
apple = 'apple'
print(f'{apple}')
```

## 이진트리

```python
while not a==b:
    if a>b: a //= 2
    else: b //= 2
print(a)
```

## Heap

- 우선순위가 가장 높은 값을 root 노드로
- priority queue(우선순위 큐)

## BST(Binary Search Tree)

- python: set, dict(hash 역할)

## 기타 메모.
### map 함수

```python
map(function, iterable)
```

- Return an iterator that applies function to every item on iterable, yielding the results.
- 함수와 이터러블을 입력으로 받아, 입력받은 자료형의 각 요소를 함수 function이 수행한 결과를 묶어서 돌려주는 함수이다.

> 예시 입력: 6 7 1 1 2 3 6

```python
lst = sorted(list(map(int, input().split())))
```

=> 결과: lst = [1, 1, 2, 3, 6, 6, 7]

### split 함수

```python
str.split() # 괄호 안의 파라미터는 문자열을 리스트로 쪼개는 구분자로 사용
str.split(',')  # '1, ,2' -> ['1', ' ', '2']
str.split('<>') # '1<>2<>3' -> ['1', '2', '3']
```
