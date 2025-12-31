---
date created: 2024-12-09
date modified: 2025-12-31
tags: [프로그래밍, 논리, 인과]
---

# 반응형과 함수형과 논리식

## 논리식 그대로 구현하기

{x, y}라는 인자들이 있고 {a, b}라는 결과들이 있음. 시스템적으로는 이것들이 서로 관련이 없음.

콜백 방식으로 코딩을 한다고 해보면 이런 형태가 나올 것임. 시스템은 x, y 가 서로 관련있다고 보지 않기 때문에 하나의 함수의 인자로 모아 불러주지 않는다. 또 모든 이벤트 함수가 서로 비동기로 워커 스레드에서 불린다.

_example0:_
```c
def cacheX
def cacheY

def event(x) {
	cacheX = x

	a = function00(x, cacheY)
	b = function03(function02(function01(x, cacheY)))
}

def event(y) {
	cacheY = y

	a = function00(cacheX, y)
	b = function03(function02(function01(cacheX, y)))
}
```

관념을 변경하지 않고 그대로 rx 로 짠다면 다음과 같이 될 것이다. 시스템도 rx 인터페이스로 이벤트를 내어 준다고 해보자.

_example1:_
```c
def cacheX
def cacheY

x
	.doOnNext { x -> cacheX = x }
	.zipWith { cacheY }
	.flatMap { (x, y) -> (function00(x, y), function03(function02(function01(x, y)))) }
	.subscribe { (first, second) ->
		a = first
		b = second
	}

y
	.doOnNext { y -> cacheY = y }
	.zipWith { cacheX }
	.flatMap { (x, y) -> (function00(x, y), function03(function02(function01(x, y)))) }
	.subscribe { (first, second) ->
		a = first
		b = second
	}
```

딱 보기에도 근본이 없다. 그럼 어떻게 해야 하나?
a, b 의 정의를 생각해보자.

_logic2:_
```plaintext
a := function00(x, y)

b := function03(function02(function01(x, y)))
```

잠깐, 현실을 다루는 코드는 추상을 다루는 수학보다 엄밀한 구석이 있어, 타입 처리를 분명하게 짚고 넘어가겠다.

_logic2-1:_
```plaintext
p0 := (x, y)
a := function00(p0)

p1 := (x, y)
b := function03(function02(function01(p1)))
```

이때 초점이 맞춰지는 것은 정의역의 구성 요소들인 x, y 가 아니라 치역의 요소들인 a, b 이다.

_logic2-2:_
```plaintext
// 이 관념으로 볼 게 아니라,
x -> a, b
y -> a, b

// 이 관념으로 봐야 한다는 것이다. 수식의 표현과 똑같이.
a <- x, y
b <- x, y
```

이제 이걸 그대로 옮기는 것이야말로 제대로 된 구현이 아닐까?

_example2:_
```c
zip(x, y)
	.flatMap { p0 -> function00(p0) }
	.subscribe { emit ->
		a = emit
	}

zip(x, y)
	.flatMap { p1 -> function03(function02(function01(p1))) }
	.subscribe { emit ->
		b = emit
	}
```

논리와 코드의 관계를 제대로 이해 못했다면 어딘가에 example1 과 같은 코드를 짜 뒀을 가능성이 있다.

## 메소드 체이닝 = 합성함수

그런데 내 모니터로는 가로로 50 글자 밖에 못 본다.
따라서 다시 논리로 돌아가서

_logic3:_
```plaintext
p0 := (x, y)
a := function00(p0)

p1 := (x, y)
b0 := function01(p1)
b1 := function02(b0)
b := function03(b1)
```

이때 메소드 연계 호출을 다루는 관념은 합성함수의 개념과 비슷한 것이다.

_logic3-1:_
```plaintext
y = g(f(x))
  = (g * f)(x)
```

논리 기호와 코드는 표기만 다르게 하고 있을 뿐이지 의미는 그냥 1:1 대응된다. 메소드 체이닝도 단지 전위 표기인가 후위 표기인가 하는 차이일 뿐이다.

_logic3-2:_
```plaintext
y = g(f(x))
  = x.f.g
```

즉 a, b 의 정의는 다음과 같은 것이다.

_logic3-3:_
```plaintext
a := (x, y).function00

b := (x, y).function01.function02.function03
```

따라서, 논리를 정말 군더더기 없이 그대로 코드에 옮기면 이렇게 된다.

_example3:_
```c
zip(x, y)
	.function00()
	.subscribe { emit ->
		a = emit
	}

zip(x, y)
	.function01()
	.function02()
	.function03()
	.subscribe { emit ->
		b = emit
	}
```

이때 example2 에서 그냥 코드를 짜깁기해서 example3 이 되는 게 아니다. 사실은 logic2 에서 논리 수정 logic3 을 거쳐서 example3 이 나온다. 실제로 프로그래머의 사고 흐름이 이래야 한다.

_logic3-4:_
```plaintext
logic2 -> example2 (O)

example2 -> example3 (X)

logic2 -> logic3 (O)
logic3 -> example3 (O)
```

## 함수형 + 시간 + 병렬성 = 반응형

y 값이 다음과 같이 정의된다고 해보자.

_logic4:_
```
y := function03(function02(function01(x)))

y := x.function01.function02.function03
```

현실 문제는 역시 시간과 병렬성에 대해 무시할 수 없다. 앞에서 말했듯이 현실을 다루다 보면 수학보다 엄밀한 구석이 있다. function01 은 I/O 스레드에서 수행해야 하고, function02 다음에 딜레이가 1초 있어야 하며, function03는 메인 스레드에서 수행해야 한다면?

_logic4-1:_
```
y := x.subscribeOn(Io).function01.function02.delay(1).observeOn(Main).function03
```

이 또한 군더더기 없이 그대로 코드로 옮겨야 한다.

_example4:_
```c
x
	.subscribeOn(Io)
	.function01()
	.function02()
	.delay(1)
	.observeOn(Main)
	.function03()
	.subscribe { emit ->
		y = emit
	}
```
