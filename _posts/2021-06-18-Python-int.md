---
layout: post
title:  "Python integer"
date:   2021-06-18 23:10:12 +0900
categories: Python
overview: "Python은 숫자를 어떻게 표현할까"
---

## 임의 정밀도

---

임의 정밀도 정수형이란 쉽게 말해 무제한 자릿수를 제공하는 정수형을 말한다.

정수를 숫자의 배열로 간주하여, 자릿수 단위로 쪼개어 배열 형태로 표현한다.

파이썬은 10진수를 2의 30승 진수로 변경하여 한 자리수가 나타낼 수 있는 최대값은 (2**30) - 1 이다. 플랫폼에 따라 파이썬은 30비트의 자리수를 가진 32비트의 부호 없는 정수 배열 또는 15 비트의 자리수를 가진 16 비트의 부호 없는 정수 배열을 사용한다.

123456789101112131415의 표현식은 다음과 같다:

```python
(437976919 * 2**(30*0)) + (87719511 * 2**(30*1)) + (107 * 2**(30*2))
a = (437976919 * 2**(30*0))
b = (87719511 * 2**(30*1))
c = (107 * 2**(30*2))

print(a + b + c)

123456789101112131415
```

임의 정밀도 산술은 왜 32비트와 16비트를 채워서 쓰지 않는걸까?

그 이유는 다음과 같다

```python
	 - long_pow() requires that PyLong_SHIFT be divisible by 5
   - PyLong_{As,From}ByteArray require that PyLong_SHIFT be at least 8
   - long_hash() requires that PyLong_SHIFT is *strictly* less than the number
     of bits in an unsigned long, as do the PyLong <-> long (or unsigned long)
     conversion functions
   - the Python int <-> size_t/Py_ssize_t conversion functions expect that
     PyLong_SHIFT is strictly less than the number of bits in a size_t
   - the marshal code currently expects that PyLong_SHIFT is a multiple of 15
   - NSMALLNEGINTS and NSMALLPOSINTS should be small enough to fit in a single
     digit; with the current values this forces PyLong_SHIFT >= 9
```

그렇다면 임의 정밀도 산술을 부호 없는 정수 배열을 어떻게 음수로 표현할까? 보통의 2의 보수법을 사용하는 방식과는 다르게, 정수의 부호는 ob_size 필드에 별도로 저장된다. 이 필드는 ob_digit 배열의 크기를 저장한다. 크기가 2인 배열의 부호를 바꾸기 위해선 ob_size를 -2로 변경하면 된다.

```python
/* 큰 정수 표현법.
   절댓값은 다음과 같이 구할 수 있다.
	   SUM(for i=0 through abs(ob_size)-1) ob_digit[i] * 2**(SHIFT*i)
   ob_size < 0이면 음수를 나타낸다.
   ob_size == 0이면 0을 나타낸다.
   정규화된 수의 경우, ob_digit[abs(ob_size)-1] (최상위 숫자)은 0이 될 수 없다. 또한, 
	 모든 유효한 i 값에 대해 다음이 성립한다. (MASK는 1 << 30 - 1 또는 1 << 15 - 1)
   0 <= ob_digit[i] <= MASK
   할당 함수는 ob_digit[0] ... ob_digit[abs(ob_size)-1]을 실제로 사용할 수 있도록 추가 메모리를 
	 할당을 신경써야 한다.
```

```python
SHIFT = 30  # number of bits for each 'digit'
MASK = (2 ** SHIFT)
# 2**30 == 1073741824
bignum = 18446744073709551615

def split_number(bignum):
    t = abs(bignum)
    print('split number = {}'.format(t))

    num_list = []
    while t != 0:
        # Get remainder from division
        print('t = {}'.format(t))
        small_int = t % MASK  # more efficient bitwise analogue: (t & (MASK-1))
        print('small_int = {}'.format(small_int))
        num_list.append(small_int)
        print('num_list = {}'.format(num_list))

        # Get integral part of the division (floor division)
        t = t // MASK  # more efficient bitwise analogue: t >>= SHIFT

    return num_list

def restore_number(num_list):
    bignum = 0
    for i, n in enumerate(num_list):
        bignum += n * (2 ** (SHIFT * i))
    return bignum

num_list = split_number(bignum)
assert bignum == restore_number(num_list)

import ctypes

class PyLongObject(ctypes.Structure):
    _fields_ = [("ob_refcnt", ctypes.c_long),
                ("ob_type", ctypes.c_void_p),
                ("ob_size", ctypes.c_ulong),
                ("ob_digit", ctypes.c_uint * 3)]

bignum = 18446744073709551615

# for d in PyLongObject.from_address(id(bignum)).ob_digit:
#     print('')

def add_bignum(a, b):
    z = []

    if len(a) < len(b):
        # Ensure a is the larger of the two
        a, b = b, a

    carry = 0

    for i in range(0, len(b)):
        carry += a[i] + b[i]
        z.append(carry % MASK)
        carry = carry // MASK

    for i in range(i + 1, len(a)):
        carry += a[i]
        z.append(carry % MASK)
        carry = carry // MASK

    z.append(carry)

    # remove trailing zeros
    i = len(z)
    while i > 0 and z[i-1] == 0:
        i -= 1
    z = z[0:i]

    return z

print(2 ** 30)
a = 8223372036854775807
b = 100037203685477
assert restore_number(add_bignum(split_number(a), split_number(b))) == a + b
```

### 레퍼런스 카운트(Reference Counts)

---

파이썬은 메모리 관리를 위해 레퍼런스 카운트와 가비지 콜렉션을 사용한다. 파이썬은 객체로 이루어진 언어이며 모든 객체에 카운트를 포함하고, 이 카운트는 객체가 참조될 때 증가한다. 이러한 카운터가 0일 경우 메모리 할당이 삭제된다

파이썬에서 어떤 값은 어떤 타입의 객체입니다. 예를 들어 int 타입의 객체, float 타입의 객체, str 타입의 객체를 예를 들 수 있습니다. 다음 코드에서 a라는 변수는 int 타입의 객체 3을 바인딩합니다. int 타입의 객체 3이 메모리에 할당되고 이를 변수 a가 바인딩합니다. 변수가 어떤 객체를 바인딩하면 객체의 레퍼런스 카운트 값이 증가합니다.

```
>>> a = 3
>>>import sys
>>> sys.getrefcount(a)
>>> 42

```

위 코드를 실행해봅시다. a가 바인딩하는 객체인 '3'의 레퍼런스 카운트가 화면에 출력됩니다. 꽤 큰 값이 나오는데 이는 이미 파이썬 인터프리터가 실행될 때 내부에서 3이라는 값을 여러번 참조했기 때문입니다.

다음과 같이 b와 c라는 변수도 3을 바인딩해봅시다. 3이라는 객체 입장에서 생각해보면 자기 자신을 b와 c라는 새로운 변수가 바인딩하는 것이 되며 이 경우 객체 내부에 저장된 레퍼런스 카운트 값이 증가합니다. 3에 대한 레퍼런스 카운트 값을 출력해보면 44로 42에서 2만큼 증가된 것을 확인할 수 있습니다.

```
>>> b = 3
>>> c = 3
>>> sys.getrefcount(3)
44

```

이번에는 리스트 객체에 대해서 동일하게 코드를 실행해 봅시다. 출력 값을 살펴보면 2가 나오는 것을 확인할 수 있습니다. sys 모듈의 getrefcount 함수가 호출되면서 해당 객체를 참조하므로 1만큼 증가된 레퍼런스 카운트가 출력됩니다. 따라서 실제로는 `[0, 1, 2]`라는 리스트 객체에 대한 레퍼런스 카운트는 1이라고 생각하면 됩니다.

```
>>> a = [0, 1, 2]
>>>import sys
>>> sys.getrefcount(a)
2
```

참고자료 :

[위키독스](https://wikidocs.net/69557)

[[Python] 파이썬의 메모리 관리](https://dc7303.github.io/python/2019/08/06/python-memory/)


[[기초 파이썬] 파이썬의 모든 것은 Object이다 (정수편)](https://ahracho.github.io/posts/python/2017-05-01-everything-in-python-is-object-integer/)

[Why Python is Slow: Looking Under the Hood](http://jakevdp.github.io/blog/2014/05/09/why-python-is-slow/)

[https://rushter.com/blog/python-integer-implementation/](https://rushter.com/blog/python-integer-implementation/)

[https://mingrammer.com/translation-cpython-internals-arbitrary-precision-integer-implementation/](https://mingrammer.com/translation-cpython-internals-arbitrary-precision-integer-implementation/)

[https://github.com/python/cpython/blob/c5bace2bf7874cf47ef56e1d8d19f79ad892eef5/Include/longintrepr.h#L70](https://github.com/python/cpython/blob/c5bace2bf7874cf47ef56e1d8d19f79ad892eef5/Include/longintrepr.h#L70)