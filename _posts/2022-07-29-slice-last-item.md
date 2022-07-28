---
layout: post
title: "파이썬의 슬라이스는 왜 마지막 항목을 포함하지 않을까"
date: 2022-07-29 00:47:22 +0900
categories: Python
overview: "[전문가를 위한 파이썬]을 읽고 정리한 내용"
---

# 파이썬의 슬라이스는 왜 마지막 항목을 포함하지 않을까

전문가를 위한 파이썬이라는 책을 읽다가 재밌는 부분을 발췌해봤습니다.

### 파이썬의 슬라이스와 range는 왜 마지막 항목을 제외할까?

---

슬라이스와 range에서 마지막 항목을 제외하는 Pythonic 컨벤션은 0에서 시작하는 인덱싱을 사용하는 여러 언어에서 잘 작동되고 있다.

컨벤션은 다음과 같다

- 끝나는 위치만 주어졌을 때 길이를 확인하기 쉽다
  raneg(3), my_list[:3]은 둘다 길이가 3이라고 계산하기 쉽다.
- 시작하는 위치와 끝나는 위치가 주어지면 두 위치를 빼면 길이가 된다
  range(start, stop), my_list[start : stop] 의 경우 stop - start이 길이가 된다
- 슬라이스를 사용해서 자료를 나눌때 겹치는 부분이 없어서 간편하다.
  my_list는 my_list[x:]와 my_list[:x]로 나눌수 있다.

이러한 컨벤션은 네덜란드의 컴퓨터 과학자 다익스트라에 의해 주장되었다. [“Why numbering Should Start at Zero”](https://www.cs.utexas.edu/users/EWD/transcriptions/EWD08xx/EWD831.html) 라는 제목으로 작성한 짧은 메모는 수학의 표기법에 대한 내용지만, 관련성이 있다.

메모의 내용을 축약하자면

자연수 수열 2에서 12를 표기할 때, 우리는 아래의 네 가지 선택지가 있다.

```python
a)		2 ≤ i < 13
b)		1 < i ≤ 12
c)		2 ≤ i ≤ 12
d)		1 < i < 13
```

이 중에서 a) 선택지(첫 항목에서 시작해서 마지막 항목을 포함하지 않는)를 선택해야 하는 이유를 합리적으로 정리한 내용이다.

자료를 찾아봐서 번역하려고 했는데 잘 번역해주신 글이 있어서 밑에 링크를 남기겠다.

[https://shoark7.github.io/programming/knowledge/why-numbering-should-start-at-zero-kr](https://shoark7.github.io/programming/knowledge/why-numbering-should-start-at-zero-kr)
