---
layout: post
title:  "Python compile"
date:   2021-06-29 23:10:12 +0900
categories: Python
overview: "파이썬 언어의 특징에 대한 글"
---

파이썬은 인터프리터 언어이며, 고레벨, 범용적 프로그래밍 언어이다. 동적 타이핑과 가비지 컬렉션을 사용한다.

### 동적 타이핑

---

파이썬은 동적 타이핑을 사용하여 자료의 데이터 타입을 지정하지 않고 자유롭게 사용할 수 있다.

파이썬과 C++ 같은 언어 간의 속도 차이는, 정적 형식의 언어가 컴파일러에게 연산과 메모리 액세스를 최적화할 수 있도록 프로그램 구조와 데이터에 대한 엄청난 정보를 제공하기 때문이다. C++는 변수의 자료형이 정해져 있기 때문에 프로그램을 실행하기도 전에 변수를 조작하는 최적의 방법을 결정할 수 있다. 반면에 파이썬은 인터프리터가 각 행에 도달할 때까지 런타임은 변수의 값을 알지 못한다.

이는 구조의 입장에서 매우 중요한데, C++에서 컴파일러는 컴파일 하는 동안 구조의 크기와 메모리 내의 모든 필드의 위치를 알 수 있다. 따라서 데이터의 사용 방법을 계산하는 데 큰 작용을 하고, 이러한 계산에 따라 데이터를 최적화할 수 있지만 파이썬의 경우에는 불가하다.

> 동적 타이핑보다 정적 타이핑

프로그래머들 사이에서는 동적 타이핑의 편리함보다는 정적 타이핑이 주는 이점으로, 정적 타이핑을 선호하기도 한다.
런타임 속도, 초기화 속도, 시제품 능력, 내부 장애 가능성, 정확성 확인, 정적 분석, 지지 체인 등에서 동적 타이핑은 정적 타이핑에 비해 좋지 못한 퍼포먼스를 보여준다. 아직은 생소한 개념들이어서 나중에 포스팅하겠다.


### 인터프리터 언어

---

C/C++로 프로그램을 작성할 때는 컴파일 과정을 행해야한다. 컴파일에는 사용자가 이해할 수 있는 고급 언어를 바이트 코드(byte code)  또는 기계 코드(machine code)로 변환하는 작업이 포함된다. 

인터프리터 언어의 경우 실행 시마다 코드를 한 줄씩 기계어로 번역하는 방식이기 때문에 실행 속도는 컴파일 언어보다 느리다. 하지만 프로그램 수정이 간단하고, 컴파일러의 소스 코드를 번역해서 다시 실행파일을 만드는 시간을 절약할 수 있다.

파이썬의 경우 소스 코드를 바이트 코드로 컴파일 한 후 인터프리터를 실행시킨다. 바이트 코드는 소스 코드를 기계어에 가까우면서 플랫폼 독립적으로 표현한 것이지만, 이진 코드가 아니며 시스템에서 직접 실행할 수 없다.

컴파일 후 실행을 위해 바이트 코드가 PVM으로 전송된다. PVM(파이썬 Virual Machine)은 바이트 코드를 실행하는 인터프리터이며 파이썬 시스템의 일부이다.

> 컴파일과 인터프리터를 동시에 사용하는 이유

자바를 개발한 엘런 고슬링은 각각의 운영체제에서 다른 방식의 컴파일을 하는 것을 낭비라고 생각했다. 그래서 소스 코드를 바이트 코드로 컴파일한 후 인터프리터를 실행하여 플랫폼에 독립적인 언어를 만들었다.
파이썬의 귀도에 관한 글은 찾지 못했지만 같은 이유라고 생각한다.
근시일 내에 ([JIT 컴파일러](https://ko.wikipedia.org/wiki/JIT_%EC%BB%B4%ED%8C%8C%EC%9D%BC))가 좋은 답이 되어줄 것 같다.
 

### 바이트 코드와 기계 코드

---

바이트 코드는 소스 코드와 기계 코드의 중간 코드이다.  바이트 코드는 인터프리터에 의해 기계 코드로 변환된 후 기계에서 읽을 수 있다. 바이트 코드 자체로는 실행 불가능한 코드이며, 모든 시스템에서 운영체제와 상관없이 PVM에서 실행되도록 컴파일된다.

기계 코드는 시스템에서 직접 이해할 수 있는 명령 집합이며 CPU에서 처리한다. 기계 코드는 바이트 코드 및 소스 코드와 완전히 다른 이진 형식이다. 소스 코드의 가장 낮은 수준으로, 기계 코드는 컴파일의 결과물이다.

![../../../../../public/assets/2021-07-08-python-compile/disassembly.jpg](../../../../../public/assets/2021-07-08-python-compile/disassembly.jpg)

위의 그림은 dis 모듈을 사용하여 소스 코드를 바이트 코드로 변환한 것이다([disassembler](https://ko.wikipedia.org/wiki/%EC%97%AD%EC%96%B4%EC%85%88%EB%B8%94%EB%9F%AC)). 이러한 바이트 코드는 Python Virtual Machine (PVM)에 의해 인터프리터로 해석된다.

```
dis.dis('for _ in x: pass')

1  |    2  |  3 | 4    |                 5 | 6
---------------------------------------------------------------
  1           0 LOAD_NAME                0 (x)
              2 GET_ITER
        >>    4 FOR_ITER                 4 (to 10)
              6 STORE_NAME               1 (_)
              8 JUMP_ABSOLUTE            4
        >>   10 LOAD_CONST               0 (None)
             12 RETURN_VALUE
```

1번 섹션은 소스코드의 행을 뜻한다. 1줄 뿐인 코드여서 1만 표시되어있다.

2번 섹션의 >> 는 점프를 뜻한다. 4번 FOR_ITER 에서 10번 LOAD_CONST로 점프한다.

3번 섹션은 명령의 인덱스로, 바이트 코드 카운터이다. 

4번 섹션은 명령의 이름을 뜻한다, LOAD_NAME의 경우 로컬 변수, 즉 0번째 로컬 변수인 (x)를 뜻한다.

- 인터프리터의 스택 시스템에서 0번째 인덱스에 있는 변수의 이름..!

[How exactly is python Bytecode Run in Cpython?](https://stackoverflow.com/questions/19916729/how-exactly-is-python-bytecode-run-in-cpython)

5번 섹션은 4번 섹션으로 부터의 명령의 인자이다.

6번 섹션은 사람이 읽을 수 있게 표현한 명령 인자이다.

[[python] understanding output of dis - Programmer Sought](https://www.programmersought.com/article/39714880936/)

파이썬은 컴파일을 실행할 경우 소스코드를  바이트 코드로 변경한다. 이러한 바이트 코드를 바이트로 나타내기 위해서 co_code 명령어를 사용한다.

![../../../../../public/assets/2021-07-08-python-compile/bytecode.jpg](../../../../../public/assets/2021-07-08-python-compile/bytecode.jpg)

Windows 운영체제의 경우 C++ 언어의 소스를 컴파일시 .exe 파일을 생성한다. 이는 Widnows 운영 체제에서 실행하는 방법을 알고 있는 바이너리 코드이기 때문에 다른 운영체제에서는 사용할 수 없다.

하지만 파이썬 언어의 소스를 컴파일시 bytecode를 생성하고 이를 인터프리터 언어로 한줄 한줄 해석하기 때문에 다른 언어에 비해 느리게 실행된다.

![../../../../../public/assets/2021-07-08-python-compile/pvm.png](../../../../../public/assets/2021-07-08-python-compile/pvm.png)
파이썬의 컴파일 순서는 다음과 같다

1. 파이썬 컴파일러는 소스 코드의 형식이나 명력이 올바른지, 각 줄의 구문을 확인한다. 오류가 있을 경우 변환이 즉시 중지되고 메세지가 표시된다
2. 오류가 없는 경우, 바이트 코드 라고 하는 중간 언어로 변환한다.
3. 바이트 코드가 PVM으로 전송된다. 이는 파이썬 인터프리터로  파이썬 바이트 코드를 시스템 실행 코드로 변환한다.

왜 파이썬은 인터프리터와 컴파일을 둘다 사용할까

인터프리터만 사용할 경우 속도가 느리기 때문에 사실상 모든 인터프리터 언어는 실제로 소스 코드를 내부 표현으로 컴파일하여 코드를 반복적으로 구문 분석할 필요가 없다.

Python Language Specification 에는 인터프리터와 컴파일에 대한 구별을 명확히 하고 있지 않다. 필요한 것을 사용하면 될 것이다. 바이트 코드와 기계 코드의 경우도 순수한 기계 코드로 컴파일 하는 것이 가능하다. 

하지만 이러한 경우들은 그 차이에서 생각보다 큰 퍼포먼스를 보여주지 못했고


### for 문과 while 문의 속도 차이

---

원본 자료 : [https://www.programmersought.com/article/39714880936/](https://www.programmersought.com/article/39714880936/)

좋은 글이 있어서 번역해왔습니다.

![../../../../../public/assets/2021-07-08-python-compile/forwhile.jpg](../../../../../public/assets/2021-07-08-python-compile/forwhile.jpg)

for range반복문으로 100을 센 것과 while 반복문으로 100을 센 것의 시간 차이가 있다. 그 이유는 바이트 코드로 나타냈을 때 명확해진다.

![../../../../../public/assets/2021-07-08-python-compile/forwhile2.jpg](../../../../../public/assets/2021-07-08-python-compile/forwhile2.jpg)


4 LOAD_NAME 로컬 변수 i를 불러오고 

6 LOAD_CONST i의 상수 100과 (스택의 인덱스 1번에 숫자 100)

8 COMPARE_OP 비교하는 연산이 

10 POP_JUNMP_IF_FALSE FALSE일 경우 22번으로 점프

12 LOAD_NAME i 변수를 다시 인터프리터의 스택시스템에서 꺼내온 후 

14 LOAD_CONST 상수를 가져와서

16 INPLACE_ADD 더한다

18 STORE_NAME 변수의 값을 저장한 뒤

20 JUMP_ABSOLUTE 4번 바이트코드로 이동한다.

22 LOAD_CONST 상수를 스택에 푸쉬한다.

24 RETURN_VALUE TOS(스택 최상단)을 함수 호출자에게 반환한다.

for range 반복문의 경우 8번 인덱스에서 14번 인덱스 까지의 작업을 반복하지만 while 반복문의 경우 4번에서 22번 까지의 작업을 반복한다. 

참고자료:

[Why does python need both a compiler and an interpreter?](https://softwareengineering.stackexchange.com/questions/289429/why-does-python-need-both-a-compiler-and-an-interpreter)

[Internal working of python - GeeksforGeeks](https://www.geeksforgeeks.org/internal-working-of-python/)

[파이썬에 대하여, 파이썬은 어떻게 동작하는가? 파이썬의 장단점](https://cjh5414.github.io/about-python-and-how-python-works/)

[How does python work?](https://towardsdatascience.com/how-does-python-work-6f21fd197888)

[Difference between Byte Code and Machine Code - GeeksforGeeks](https://www.geeksforgeeks.org/difference-between-byte-code-and-machine-code/)

[Understanding python Bytecode](https://towardsdatascience.com/understanding-python-bytecode-e7edaae8734d)

[Why isn't there a python compiler to native machine code?](https://softwareengineering.stackexchange.com/questions/243269/why-isnt-there-a-python-compiler-to-native-machine-code)