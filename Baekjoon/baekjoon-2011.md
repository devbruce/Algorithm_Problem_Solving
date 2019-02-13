# 2011 (암호코드)

문제 링크: <https://www.acmicpc.net/problem/2011>

<br>

## 접근방법

- 다이나믹 프로그래밍

`D[n]` 은 입력된 암호문의 n 자릿수까지의 값을 해석할 때  
해석의 가짓수라고 정의한다.  

예로 15226 이 입력되었을 때  
`D[1]` : `1` 의 해석의 가짓수  
`D[2]` : `15` 의 해석의 가짓수  
`D[3]` : `152` 의 해석의 가짓수  
`D[4]` : `1522` 의 해석의 가짓수  
`D[5]` : `15226` 의 해석의 가짓수  
라고 할 수 있다.  

<br>

해석의 방법은 두가지가 있다.  

**i )** `D[n]` 의 마지막 수를 한 자릿수로 해석  

- `D[n] = D[n-1]`

- 한 자릿수가 0 일 경우에는 해석 불가

<br>

**ii )** `D[n]` 의 마지막 두 수를 두 자릿수로 해석  

- `D[n] = D[n-2]`

- 두 자릿수가 10 보다 작은 경우 해석 불가 (ex. `07`)  

- 두 자릿수가 26 보다 큰 경우 해석 불가 (ex. `51`)

<br>

`D[3]` 에 해당하는 `152` 의 경우를 예시로 들면  

**i )** `D[3]` 의 마지막 수를 한 자릿수로 해석  

`152` 의 마지막 수 `2` 를 한 자릿수로 해석할 경우  
`15` 에 `2` 를 추가한 형태가 된다.  
`15` 의 해석의 가짓수 (`D[2]`) 가 있을 때  
한 자리 수로 확정된 `2` 가 추가되었다고 할 수 있다.  
(현재 한 자릿수로 해석하는 경우이므로 한정적으로 맞는 명제)  

따라서 `D[3] = D[2]` 가 성립하게 된다.  

즉 아래와 같이 일반화 할 수 있다.  

```python
D[n] = D[n-1]
```

> 만약 한 자릿수로 해석한 마지막 수가 `2` 가 아닌 `0` 이었다면  
해석 불가에 해당하므로 해당 케이스에서 `D[3] = 0` 이 된다.  

<br>

**ii )** `D[3]` 의 마지막 두 수를 두 자릿수로 해석  

`152` 의 마지막 두 수 `52` 를 두 자릿수로 해석할 경우  
`1` 에 `52` 를 추가한 형태가 된다.  
`1` 의 해석의 가짓수 (`D[1]`) 가 있을 때  
두 자리 수로 확정된 `52` 가 추가되었다고 할 수 있다.  
(현재 두 자릿수로 해석하는 경우이므로 한정적으로 맞는 명제)  

따라서 `D[3] = D[1]` 가 성립하게 된다.  

즉 아래와 같이 일반화 할 수 있다.  

```python
D[n] = D[n-2]
```

그런데 이 경우에서는 일반화 시킬 수 없는 예외에 속한다.  
두 자릿수로 인식한 `52` 는 `26` 보다 큰 수 이므로 해석 불가이다.  
**따라서 해당 케이스의 최종결과는 `D[3] = 0` 이 된다.**

위의 두가지 케이스를 고려한 `D[3]` 은 두가지 경우를 합한 것과 같으므로  
아래의 식이 성립한다. 

```python
D[3] = D[2] + 0
```

예외를 무시하고 일반화 시킬 경우 아래와 같다.  

```python
D[n] = D[n-1] + D[n-2]
```

하지만 예외의 경우를 고려하면 위의 일반화식은 큰 의미가 없다.  

<br><br>

## Example

### 암호 15226

| 입력된 암호문자열 | 1 | 5 | 2 | 2 | 6 |
|------------------|---|---|---|---|---|
| 한 자릿수로 해석 | 1 | 1 | 2 | 2 | 4 |
| 두 자릿수로 해석 | 0 | 1 | 0 | 2 | 2 |
| D[n]             | 1 | 2 | 2 | 4 | 6 |

Result : 6

<br>

### 암호 20103

| 입력된 암호문자열 | 2 | 0 | 1 | 0 | 3 |
|-------------------|---|---|---|---|---|
| 한 자릿수로 해석  | 1 | 0 | 1 | 0 | 1 |
| 두 자릿수로 해석  | 0 | 1 | 0 | 1 | 0 |
| D[n]              | 1 | 1 | 1 | 1 | 1 |

Result : 1

<br><br>

## 특이사항

- `0` 또는 `0` 으로 시작되는 암호문의 예외처리

- `00` 이 들어간 암호문은 해석불가이므로 예외처리  
(접근방법에 서술된 해석불가 경우를 예외처리 했다면 경우 자동으로 처리된다.)

- `0` 이 아닌 길이가 1 인 암호문은 결과값이 1 이므로 예외처리

- 결과값에 `1000000` 으로 나눈 값을 출력

<br><br>

## Python3

Dynamic Programming (Bottom-Up)

### \# 1

1 차원 풀이

```python
def decryption_case_cnt(data):
    if data[0] == '0': return 0
    n = len(data)
    d = [0] * n
    d[0] = 1
    if n > 1:
        d[1] += d[0] if data[1] != '0' else 0 
        d[1] += 1 if int(data[0]+data[1]) <= 26 else 0

    for i in range(2, n):
        now = data[:i+1]
        if now[-1] != '0':
            d[i] += d[i-1]
        if 10 <= int(now[-2]+now[-1]) and int(now[-2]+now[-1]) <= 26:
            d[i] += d[i-2]
    return d[n-1]
    

print(decryption_case_cnt(input()) % 1000000)
```

### \# 2

2 차원 풀이

```python
def decryption_case_cnt(data):
    if data[0] == '0': return 0
    n = len(data)
    d = [[0,0] for _ in range(n)]
    d[0][0] = 1
    d[0][1] = 0
    if n > 1:
        d[1][0] = 1 if data[1] != '0' else 0
        d[1][1] = 1 if int(data[0]+data[1]) <= 26 else 0

    for i in range(2, n):
        now = data[:i+1]
        if now[-1] != '0':
            d[i][0] = sum(d[i-1])
        if 10 <= int(now[-2]+now[-1]) and int(now[-2]+now[-1]) <= 26:
            d[i][1] = sum(d[i-2])
    return sum(d[n-1])
    

print(decryption_case_cnt(input()) % 1000000)
```