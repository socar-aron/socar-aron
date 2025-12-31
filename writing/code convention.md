---
date created: 2024-01-05
date modified: 2025-07-31
---

# 코드 컨벤션은 어떤 것이어야 하는가

## 컨벤션은 가능한 선택지 중에서 아무거나 고르는 게 아니다

코드 작업은 기본적으로 글을 다루는 것이며, 따라서 가독성은 곧 생산성으로 연결된다. 내가 어떤 환경에서 작업을 하고 있다면, 주변과 같은 컨벤션으로 작업물을 구성해야 높은 생산성을 유지할 수 있다. 또 근거가 있으면서도 최대한 단순한 규칙이어야 하며, 코드 전체가 일관되게 작성되어 있어야 한다. 따라서 높은 생산성을 유지하고자 하는 프로젝트의 컨벤션은 자유로운 것이 아니라 무엇보다도 부자유스런 것이다.

우선, 위계로서 상위(깊이로서는 로우 레벨, 공간적으로는 바깥 테두리)에 있는 컨벤션을 준수해야한다. 텍스트 파일 형태로 작성하는 모든 소스는, 텍스트 파일에 대한 일반적인 컨벤션을 따라줘야 한다. 예를 들어 마지막 줄을 비우는 것(EOF 직전에 개행문자). 또 소스 코드이기 때문에 적용하는 일반적인 컨벤션도 따라 줘야 한다. 예를 들어 모든 공백을 스페이스만으로 구성하는 것. Kotlin 이면 Java 의 컨벤션도 감안해서 구성해야 한다. iOS 라면 Foundation 외 표준 라이브러리의 컨벤션과 비슷하게 구성해야 좋다.

상위 컨벤션에서 정하지 않은 요소나 자율에 부친 요소도 방치해서는 안 된다. 가독성의 논리에 맞게 명확한 기준을 만들고 적용해야 한다. 예를 들어 포인터 타입의 별표 주변에 공백을 어떻게 배치할지 같은 문제에 대해 Objective C 컨벤션에서는 명확하게 정하지 않는다. 그러나 우리 프로젝트가 높은 생산성을 확보하고자 한다면, 최대한 분명한 근거를 사용하여 이런 문제를 확정해야 한다.

## 그렇다면 근거로는 어떤 것들을 고려해야 하는가?

### Case 0

예를 들어 인덴트에 대한 일반적인 결정을 생각해보자.

```
// 이런 메소드의 이름이나 인자가 길어질 때를 대비하여 대개 내려쓰기를 할 수 있다.
function doSomething(argument0, argument1)

// A 방식으로 정할 수도 있고
function doSomething(argument0,
                     argument1)

// B 방식으로 정할 수도 있으며
function doSomething(
    argument0,
    argument1
)

// 또한 A, B 간에 기능적으로 우열성이 없다고 해보자.
```

위와 같은 상황에서 A 가 좋은가 B 가 좋은가를 생각해보고 둘 중에 하나로 정해야 한다. 그러면 메소드 이름과 인자 이름, 인자 개수가 확장적이라는 점을 생각해야 한다. 이것들이 모두 늘어날 때 어떤 상황이 될 것인가.

```
// A 방식
function doSomethingOnSituationDangerouseOrSafe(argumentOfThisMethodOrSomeOthers0000,
                                                argumentOfThisMethodOrSomeOthers0001,
                                                argumentOfThisMethodOrSomeOthers0002,
                                                argumentOfThisMethodOrSomeOthers0003,
                                                argumentOfThisMethodOrSomeOthers0100,
                                                argumentOfThisMethodOrSomeOthers0101,
                                                argumentOfThisMethodOrSomeOthers0102,
                                                argumentOfThisMethodOrSomeOthers0103)

// B 방식
function doSomethingOnSituationDangerouseOrSafe(
    argumentOfThisMethodOrSomeOthers0000,
    argumentOfThisMethodOrSomeOthers0001,
    argumentOfThisMethodOrSomeOthers0002,
    argumentOfThisMethodOrSomeOthers0003,
    argumentOfThisMethodOrSomeOthers0100,
    argumentOfThisMethodOrSomeOthers0101,
    argumentOfThisMethodOrSomeOthers0102,
    argumentOfThisMethodOrSomeOthers0103
)
```

양상은 명확해보인다. Order 와 비슷한 개념으로 생각해보자. 메소드 이름 m, 인자 길이 a, 인자 개수 c 라고 해보자. 세로 방향으로는 두 방식 모두 O(c) 공간을 차지하여 이렇다할 차이가 없다. 그러나 A 방식은 가로 방향으로 O(m+a) 공간을 차지하며 B 방식은 O(max(m, a)) 공간을 차지한다. 문장을 쓰는 데 있어서 가로쓰기를 하는 문화권은 가로 방향 길이를 제한하고 세로로 무한 확장한다. 세로쓰기를 하는 문화권은 세로 방향 길이를 제한하고 가로로 무한 확장한다. 코드를 세로쓰기할 게 아닌 이상, 가로 방향 길이 제한 컨벤션이 있을 것이다. 그렇다면 가로 길이가 늘어날 위험성이 적은 B 방식이 좋은 컨벤션이다. 물론 이 예시는 툴이나 시스템의 간섭이 없는 것을 전제로 한다. 예를 들어, 만일 환경에서 제공되는 자동 포맷팅이 A 밖에 없다면 A 를 택하는 것이 생산성에 도움이 될 것이다.

### Case 1

다른 예시를 보자. 여는 중괄호의 내려쓰기를 어떻게 하는 것이 좋은가?

```
// B 방식으로 정할 수도 있고
function doSomething(argument0) {
    statement0
    statement1
}

// C 방식으로 정할 수도 있다.
function doSomething(argument0)
{
    statement0
    statement1
}

// B, C 간에 자동 포맷팅에서는 우열성이 없다고 해보자.
```

코드 에디터에 별다른 기능이 없던 시절에는 C 방식이 사람의 눈으로 보기에 괄호 매칭을 확인하기 편했다고 볼 수 있다. 그러나 요즘의 에디터는 대개 folding 을 지원한다. 만일 코드 folding 을 잘 사용한다면 statement 의 구조에 맞게 필요최소한의 위치에만 내려쓰기를 허용해야 한다는 것이 어떤 것인지 이해할 것이다. 함수 본문을 fold 한 경우 다음과 같이 차이가 나게 된다.

```
// B 방식 folding
function doSomething(argument0) {...}

// C 방식 folding
function doSomething(argument0)
{...}
```

if-else 의 body 에도 일관된 규칙을 적용하게 될텐데, 그러면 C 방식은 구문의 실제 구조와 조화되지 않는다는 점이 더욱 잘 부각된다.

```
// B 방식 folding
if (condition) {...} else {...}

// C 방식 folding
if (condition)
{...}
else
{...}
```

B 방식은 구문의 실제 구조와 일치하며, 그렇기 때문에 구문 해석을 기반으로 하는 부가 기능과 잘 맞물릴 수 있다는 것이다. 따라서 B 가 C 보다 좋은 컨벤션임을 알 수 있다.

### Case 2

사실 위 두 예시는 완전히 무관하지 않다. 소괄호의 내려쓰기 규칙이 중괄호의 내려쓰기 규칙과 다를 이유가 무엇인가. 이것 역시 같은 것으로 볼 수 있다면 소괄호의 내려쓰기에서 이런 방식도 고려했어야 한다.

```
// C 방식. 물론 두 번째 예시에서 설명한 이유로 이것이 좋은 방식은 아닐 것이다.
function doSomething
(
    argument0,
    argument1
)
```

마찬가지로 중괄호의 내려쓰기에서도 이런 방식을 생각할 수 있다.

```
// A 방식. 이건 정말 가당치도 않게 보인다...
function doSomething(argument0) { statement0
                                  statement1 }
```

결국 일관성 측면에서도 Case 0, 1 에서 각각 B 로 지칭한 방식이 나은 것이다.

Case 0, 1 을 통합하고 A, B, C 각각을 모두 일관되게 적용하면 다음과 같이 비교할 수 있다.

```
// A 방식.
function doSomethingNameIsVeryLong(argumentNameIsVeryLong0,
                                   argumentNameIsVeryLong1,
                                   argumentNameIsVeryLong2,
                                   argumentNameIsVeryLong3,
                                   argumentNameIsVeryLong4) { statementNameIsVeryLong0
                                                              statementNameIsVeryLong1
                                                              statementNameIsVeryLong2
                                                              statementNameIsVeryLong3
                                                              statementNameIsVeryLong4 }

// A 방식 folding.
function doSomethingNameIsVeryLong(...) {...}

// B 방식
function doSomethingNameIsVeryLong(
    argumentNameIsVeryLong0,
    argumentNameIsVeryLong1,
    argumentNameIsVeryLong2,
    argumentNameIsVeryLong3,
    argumentNameIsVeryLong4
) {
    statementNameIsVeryLong0
    statementNameIsVeryLong1
    statementNameIsVeryLong2
    statementNameIsVeryLong3
    statementNameIsVeryLong4
}

// B 방식 folding.
function doSomethingNameIsVeryLong(...) {...}

// C 방식
function doSomethingNameIsVeryLong
(
    argumentNameIsVeryLong0,
    argumentNameIsVeryLong1,
    argumentNameIsVeryLong2,
    argumentNameIsVeryLong3,
    argumentNameIsVeryLong4
)
{
    statementNameIsVeryLong0
    statementNameIsVeryLong1
    statementNameIsVeryLong2
    statementNameIsVeryLong3
    statementNameIsVeryLong4
}

// C 방식 folding.
function doSomethingNameIsVeryLong
(...)
{...}
```

결국 B 만이, 줄 수와 행 수를 모두 적절하게 절약하면서 일관성도 있고 널리 쓰이고 있는 방식이다.
