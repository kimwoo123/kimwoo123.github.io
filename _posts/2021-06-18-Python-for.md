---
layout: post
title:  "Python for loop"
date:   2021-06-18 23:10:12 +0900
categories: Python
overview: "Python의 for 반복문은 어떻게 동작할까?"
---

## for 반복문의 내부

---

for 반복문은 내부적으로 다음과 같이 작동한다

```python
>>> L = [1, 2, 3]
>>> iterator = iter(L)
>>> iterator
<list_iterator object at 0x101231f28>
>>> next(iterator)
1
>>> L.pop()
3
>>> L
[1, 2]
>>> next(iterator)
2
>>> next(iterator)
Traceback (most recent call last):
  File "<input>", line 1, in <module>
StopIteration
```

for문은 iterable한 객체를 요구하는데 iterable이란 반복 가능한 객체를 뜻한다.

iterable 이란

- 반복 가능한 무언가
- for 반복문의 오른쪽에 들어갈 수 있는 것
- iter() 를 사용했을때 ITERATOR를 반환하는 것
- getitem 메소드를 활용하여 인덱스를 통한 참조가 가능한 것

iterator 란

- next 메소드를 사용 했을 떄
    - iterations의 다음 값을 반환 한다
    - next의 포인터를 갱신한다
    - StopIterations이 raising되면 끝낸다
- self-iterable에서는 self를 리턴한다

최종 적으로 

iteration 이란 여러가지 정렬된 요소 중에 꺼내어진 하나의 요소이고

iterable 은 iteration 할 수 있다. 즉 하나의 요소를 꺼낼 수 있음을 뜻한다.

iterator는 상태를 유지하며 반환할 수 잇는 마지막 값까지 원소를 필요할 때마다 하나씩 반환하는 것이다.

iterator는 상태를 갖는다. iterable에 iter 함수를 쓸 때마다 새로운 이터레이터가 생성된다. 이때 각 iterator는 서로 다른 상태를 유지하고 있다. 다시 말해 한 iterator의 동작이 다른 iterator의 동작에 영향을 미치지 않는다.

```python
assert iter(l) != iter(l)
```

iterator가 관리하는 상태란 무엇인가? 여러가지 요소 중 여기서는 각 iterator가 순회하고 있는 위치를 예로 들 수 있다.

```python
for i in arr:
	for j in arr:
```

두 iterator는 서로 다른 상태값을 유지, 관리하는 완전히 다른 객체다. 비록 같은 iterable에서 생성됐을지라도.

iterator는 iter 함수의 인자로 iterable을 적용해 반환한 객체로, 값을 하나씩 반환하며 그 상태값을 유지, 관리하는 객체라고 개념적으로 말할 수 있다.

```python
iterator = iter([1, 2, 3, 4, 5])

print(next(iterator))
print(next(iterator))
print(next(iterator))

1
2
3

for n in iterator:
    print(n)

4
5
```

for 반복문은 내부적으로 이런 기능을 하며

```python
for x in object:
# object는 iterator인가?
if object.iterator():
	self._i = object
x = _i.next()
x = _i.next()
...
```

클래스로는 밑에와 같은 기능을 한다.

```python
class Iterator(object):
    def next(self):
        pass

class Iterable(object):
    def __iter__(self):
        return Iterator()
```

```python
arr = [1, 2, 3]
이라는 iterable한 자료형 이 있을 때
__iter__(arr)
라는 메소드를 사용하여 이를
iterator로 만든다
이는 arr를 관리하는 상태로 포인터의 위치 등을 가지고 있다.
여기에 next를 반복하여 iterator가 끝날 때 까지 iteration을 반환한다
iterator가 끝나면 Stopiterations를 반환하고 종료한다.
```

참고자료 : 

[[Python] Iterable, Iterator 그리고 Generator](https://shoark7.github.io/programming/python/iterable-iterator-generator-in-python#3a)

[How does Python manage a 'for' loop internally?](https://stackoverflow.com/questions/43206541/how-does-python-manage-a-for-loop-internally)

[What exactly are iterator, iterable, and iteration?](https://stackoverflow.com/questions/9884132/what-exactly-are-iterator-iterable-and-iteration?noredirect=1&lq=1)

```python
print(next((i for i in range(10) if i**2 == 17), None))

None
```
