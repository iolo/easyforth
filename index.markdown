---
layout: default
---

<div markdown="1" class="toc">
  * toc
  {:toc}
</div>


## 소개

이 작은 전자책은 Forth라는 프로그래밍 언어를 배우기 위한 책입니다. Forth는
대부분의 다른 언어와는 다릅니다. 함수형 언어도 객체 지향 언어도 아니고,
타입 검사도 없으며, 문법도 사실상 없다시피 합니다. 1970년대에 만들어졌지만
지금도 [특정 분야](http://www.forth.com/resources/apps/more-applications.html)에서
사용되고 있습니다.

이렇게 특이한 언어를 왜 배워야 할까요? 새로운 프로그래밍 언어를 하나씩 배울 때마다
문제를 새로운 방식으로 생각하는 데 도움이 됩니다. Forth는 배우기 매우 쉽지만,
익숙한 방식과는 다르게 생각해야 합니다. 그래서 코딩의 시야를 넓히기에
아주 좋은 언어입니다.

이 책에는 제가 JavaScript로 작성한 간단한 Forth 구현이 포함되어 있습니다.
완벽한 구현은 아니며, 실제 Forth 시스템이라면 기대할 만한 기능도 많이 빠져 있습니다.
예제를 쉽게 실행해 볼 수 있도록 마련한 것입니다. (Forth 전문가라면
[여기에서 기여](https://github.com/skilldrick/easyforth)해 더 나은 구현을 만들어 주세요!)

독자가 다른 프로그래밍 언어를 적어도 하나 알고 있고, 자료 구조인 스택이
어떻게 동작하는지 기본적으로 이해하고 있다고 가정하겠습니다.


## 숫자 더하기

Forth가 대부분의 다른 언어와 구별되는 점은 스택을 사용한다는 것입니다.
Forth에서는 모든 것이 스택을 중심으로 돌아갑니다. 숫자를 입력할 때마다
스택에 그 숫자가 쌓입니다. 두 숫자를 더하려면 `+`를 입력하면 됩니다.
그러면 스택 맨 위의 두 숫자를 꺼내 더한 다음, 결과를 다시 스택에 넣습니다.

예제를 살펴봅시다. 다음 내용을 인터프리터에 직접 입력하고(복사해서 붙여 넣지 마세요),
각 줄을 입력한 뒤 `Enter` 키를 누르세요.

    1
    2
    3

{% include editor.html %}

한 줄을 입력하고 `Enter` 키를 누를 때마다 Forth 인터프리터가 그 줄을 실행하고,
오류가 없었다는 뜻으로 끝에 `ok` 문자열을 덧붙입니다. 각 줄을 실행할 때마다
위쪽 영역에 숫자가 쌓이는 것도 보일 것입니다. 이 영역은 스택을 시각적으로
보여 줍니다. 다음과 같은 모습이어야 합니다.

{% include stack.html stack="1 2 3" %}

이제 같은 인터프리터에 `+` 하나를 입력하고 `Enter` 키를 누르세요.
스택 맨 위의 두 요소인 `2`와 `3`이 `5`로 바뀝니다.

{% include stack.html stack="1 5" %}

이 시점에서 편집기 창은 다음과 같아야 합니다.

<div class="editor-preview editor-text">1  <span class="output">ok</span>
2  <span class="output">ok</span>
3  <span class="output">ok</span>
+  <span class="output">ok</span>
</div>

다시 `+`를 입력하고 `Enter` 키를 누르면 맨 위의 두 요소가 6으로 바뀝니다.
여기서 `+`를 한 번 더 입력하면, 스택에 요소가 _하나_뿐인데도 Forth는
맨 위의 두 요소를 꺼내려 합니다! 그러면 다음과 같이 `Stack underflow` 오류가 발생합니다.

<div class="editor-preview editor-text">1  <span class="output">ok</span>
2  <span class="output">ok</span>
3  <span class="output">ok</span>
+  <span class="output">ok</span>
+  <span class="output">ok</span>
+  <span class="output">Stack underflow</span>
</div>

Forth에서는 토큰마다 줄을 나눠 입력할 필요가 없습니다. 다음 편집기에
아래 내용을 입력한 뒤 `Enter` 키를 누르세요.

    123 456 +

{% include editor.html size="small"%}

이제 스택은 다음과 같아야 합니다.

{% include stack.html stack="579" %}

이처럼 연산자가 피연산자 뒤에 오는 방식을
[역폴란드 표기법](https://en.wikipedia.org/wiki/Reverse_Polish_notation)이라고 합니다.
조금 더 복잡한 계산인 `10 * (5 + 2)`를 해 봅시다. 인터프리터에 다음을 입력하세요.

    5 2 + 10 *

{% include editor.html size="small"%}

Forth의 장점 중 하나는 연산 순서가 전적으로 프로그램에 나타나는 순서에 따라
정해진다는 것입니다. 예를 들어 `5
2 + 10 *`를 실행하면, 인터프리터는 스택에 5를 넣고, 2를 넣고, 둘을 더한 결과인
7을 넣습니다. 그런 다음 10을 스택에 넣고 7과 10을 곱합니다.
따라서 우선순위가 낮은 연산을 묶기 위한 괄호가 필요하지 않습니다.

### 스택 효과

대부분의 Forth 워드는 어떤 식으로든 스택에 영향을 줍니다. 스택에서 값을 꺼내는 워드도,
새 값을 넣는 워드도, 두 가지를 모두 하는 워드도 있습니다. 이러한 "스택 효과"는
보통 `( before -- after
)` 형태의 주석으로 표현합니다. 예를 들어 `+`의 스택 효과는 `( n1 n2 -- sum )`입니다.
`n1`과 `n2`는 스택 맨 위의 두 숫자이고, `sum`은 연산 후 스택에 남는 값입니다.


## 워드 정의하기

Forth의 문법은 매우 단순합니다. Forth 코드는 공백으로 구분된 워드의 나열로 해석됩니다.
공백 문자를 제외한 거의 모든 문자를 워드에 사용할 수 있습니다. Forth 인터프리터는
워드를 읽으면 사전(Dictionary)이라는 내부 자료 구조에 해당 정의가 있는지 확인합니다.
정의가 있으면 실행하고, 없으면 그 워드를 숫자로 간주해 스택에 넣습니다.
워드를 숫자로 변환할 수 없으면 오류가 발생합니다.

아래에서 직접 확인해 보세요. 인식할 수 없는 워드인 `foo`를 입력하고
엔터 키를 누르세요.

{% include editor.html size="small"%}

다음과 같은 결과가 나타납니다.

<div class="editor-preview editor-text">foo  <span class="output">foo ?</span></div>

`foo ?`는 Forth가 `foo`의 정의를 찾지 못했고, 유효한 숫자도 아니었다는 뜻입니다.

`foo`를 직접 정의하려면 `:`(콜론)과 `;`(세미콜론)라는 두 특수 워드를 사용합니다.
`:`는 Forth에 새 정의를 만들겠다고 알리는 방법입니다. `:` 바로 뒤의 첫 워드가
정의의 이름이 되고, 나머지 워드가 `;` 직전까지 정의의 본문을 이룹니다.
관례상 이름과 본문 사이에는 공백 두 칸을 넣습니다. 다음을 입력해 보세요.

    : foo  100 + ;
    1000 foo
    foo foo foo

**주의:** `;` 워드 앞의 공백을 빠뜨리는 것은 흔한 실수입니다. Forth 워드는
공백으로 구분되고 대부분의 문자를 포함할 수 있으므로, `+;`도 유효한 워드입니다.
따라서 두 개의 별도 워드로 해석되지 않습니다.

{% include editor.html size="small"%}

아마 짐작했겠지만, 방금 만든 `foo` 워드는 스택 맨 위의 값에 100을 더할 뿐입니다.
그다지 흥미로운 기능은 아니지만, 간단한 정의가 어떻게 동작하는지 이해하는 데
도움이 되었을 것입니다.


## 스택 조작하기

이제 Forth에 미리 정의된 워드들을 살펴봅시다. 먼저 스택 위쪽의 요소를
조작하는 워드부터 알아보겠습니다.

### `dup ( n -- n n )`

`dup`는 "복제하다"라는 뜻의 duplicate를 줄인 말로, 스택 맨 위의 요소를 복제합니다.
다음 예제를 실행해 보세요.

    1 2 3 dup

{% include editor.html size="small" %}

스택은 다음과 같아집니다.

{% include stack.html stack="1 2 3 3" %}

### `drop ( n -- )`

`drop`은 스택 맨 위의 요소를 버립니다. 다음을 실행하면:

    1 2 3 drop

스택은 다음과 같아집니다.

{% include stack.html stack="1 2" %}

{% include editor.html size="small"%}

### `swap ( n1 n2 -- n2 n1 )`

`swap`은 짐작한 대로 스택 맨 위의 두 요소를 맞바꿉니다. 예를 들어 다음을 실행하면:

    1 2 3 4 swap

다음과 같은 결과가 나옵니다.

{% include stack.html stack="1 2 4 3" %}

{% include editor.html size="small"%}

### `over ( n1 n2 -- n1 n2 n1 )`

`over`는 이름만으로 동작을 짐작하기가 조금 어렵습니다. 스택 위에서 두 번째 요소를
복제해 맨 위에 올립니다. 다음을 실행하면:

    1 2 3 over

다음과 같은 결과가 나옵니다.

{% include stack.html stack="1 2 3 2" %}

{% include editor.html size="small"%}

### `rot ( n1 n2 n3 -- n2 n3 n1 )`

마지막으로 `rot`는 스택 맨 위의 _세_ 요소를 "회전"시킵니다. 위에서 세 번째 요소를
맨 위로 옮기고, 나머지 두 요소는 아래로 밀어냅니다.

    1 2 3 rot

실행 결과는 다음과 같습니다.

{% include stack.html stack="2 3 1" %}

{% include editor.html size="small"%}


## 출력하기

다음으로 콘솔에 텍스트를 출력하는 워드를 살펴봅시다.

### `. ( n -- )` (마침표)

Forth에서 가장 단순한 출력 워드는 `.`입니다. `.`을 사용하면 스택 맨 위의 값을
현재 줄의 출력에 표시할 수 있습니다. 다음 예제를 실행해 보세요.
공백을 빠뜨리지 않도록 주의하세요!

    1 . 2 . 3 . 4 5 6 . . .

{% include editor.html size="small"%}

다음과 같은 결과가 나타납니다.

<div class="editor-preview editor-text">1 . 2 . 3 . 4 5 6 . . . <span class="output">1 2 3 6 5 4  ok</span></div>

순서대로 살펴보면, 먼저 `1`을 스택에 넣은 뒤 꺼내서 출력합니다.
`2`와 `3`에도 같은 작업을 합니다. 그다음 `4`, `5`, `6`을 스택에 넣고,
하나씩 꺼내 출력합니다. 마지막 세 숫자의 출력 순서가 뒤집힌 이유는
스택이 후입선출 방식, 즉 나중에 넣은 값을 먼저 꺼내는 방식이기 때문입니다.

### `emit ( c -- )`

`emit`은 숫자를 ASCII 문자로 출력하는 데 사용합니다. `.`이 스택 맨 위의 숫자를
그대로 출력하는 것처럼, `emit`은 그 숫자에 해당하는 ASCII 문자를 출력합니다.
예를 들어 다음을 실행해 보세요.

     33 119 111 87 emit emit emit emit

{% include editor.html size="small"%}

직접 결과를 확인하는 재미를 위해 여기에는 출력을 보여 주지 않겠습니다.
같은 코드를 다음과 같이 쓸 수도 있습니다.

    87 emit 111 emit 119 emit 33 emit

`.`과 달리 `emit`은 각 문자 뒤에 공백을 출력하지 않으므로,
원하는 문자열을 자유롭게 만들어 출력할 수 있습니다.

### `cr ( -- )`

`cr`은 캐리지 리턴(carriage return)의 약자로, 줄바꿈을 출력합니다.

    cr 100 . cr 200 . cr 300 .

{% include editor.html size="small"%}

출력은 다음과 같습니다.

<div class="editor-preview editor-text">cr 100 . cr 200 . cr 300 .<span class="output">
100
200
300  ok</span></div>

### `." ( -- )`

마지막으로 문자열 출력을 위한 특수 워드인 `."`가 있습니다. `."`는 정의 안에서 사용할 때와
대화형 모드에서 사용할 때 동작이 다릅니다. `."`는 출력할 문자열의 시작을 나타내고,
`"`는 문자열의 끝을 나타냅니다. 닫는 `"`는 워드가 아니므로 공백으로 구분할 필요가 없습니다.
다음은 사용 예입니다.

    : say-hello  ." Hello there!" ;
    say-hello

{% include editor.html size="small"%}

다음과 같은 출력이 나타납니다.

<div class="editor-preview editor-text">say-hello <span class="output">Hello there! ok</span></div>

`."`, `.`, `cr`, `emit`을 조합하면 더 복잡한 출력도 만들 수 있습니다.

    : print-stack-top  cr dup ." The top of the stack is " .
      cr ." which looks like '" dup emit ." ' in ascii  " ;
    48 print-stack-top

{% include editor.html size="small"%}

실행하면 다음과 같은 출력이 나타납니다.

<div class="editor-preview editor-text">48 print-stack-top <span class="output">
The top of the stack is 48
which looks like '0' in ascii   ok</span></div>


## 조건문과 반복문

이제 재미있는 부분으로 넘어가 봅시다! Forth도 대부분의 다른 언어처럼 프로그램의 흐름을
제어하는 조건문과 반복문을 제공합니다. 다만 그 동작을 이해하려면 먼저
Forth에서 불리언을 어떻게 다루는지 알아야 합니다.

### 불리언

사실 Forth에는 불리언 타입이 없습니다. 숫자 `0`은 거짓으로, 그 밖의 숫자는
참으로 취급합니다. 다만 참을 나타내는 표준값은 `-1`입니다.
모든 불리언 연산자는 `0` 또는 `-1`을 반환합니다.

두 숫자가 같은지 확인하려면 `=`를 사용합니다.

    3 4 = .
    5 5 = .

출력은 다음과 같습니다.

<div class="editor-preview editor-text">3 4 = . <span class="output">0  ok</span>
5 5 = . <span class="output">-1  ok</span></div>

{% include editor.html size="small"%}

작다와 크다를 비교할 때는 `<`와 `>`를 사용합니다. `<`는 스택 위에서 두 번째 값이
맨 위의 값보다 작은지 확인하고, `>`는 반대로 큰지 확인합니다.

    3 4 < .
    3 4 > .

<div class="editor-preview editor-text">3 4 < . <span class="output">-1  ok</span>
3 4 > . <span class="output">0  ok</span></div>

{% include editor.html size="small"%}

논리곱(AND), 논리합(OR), 부정(NOT) 연산자는 각각 `and`, `or`, `invert`로 사용할 수 있습니다.

    3 4 < 20 30 < and .
    3 4 < 20 30 > or .
    3 4 < invert .

첫 번째 줄은 C 계열 언어의 `3 < 4 & 20 < 30`에 해당합니다.
두 번째 줄은 `3 < 4 | 20 > 30`에, 세 번째 줄은 `!(3 < 4)`에 해당합니다.

`and`, `or`, `invert`는 모두 비트 단위 연산입니다. 올바른 형태의 플래그인
`0`과 `-1`에는 예상대로 동작하지만, 임의의 숫자에 사용하면 잘못된 결과가 나옵니다.

{% include editor.html size="small"%}

### `if then`

이제 드디어 조건문을 살펴볼 수 있습니다. Forth의 조건문은 정의 안에서만
사용할 수 있습니다. 가장 단순한 조건문은 `if
then`으로, 대부분의 언어에서 쓰는 일반적인 `if` 문에 해당합니다.
다음은 `if then`을 사용하는 정의의 예입니다. 여기서는 스택 맨 위의 두 숫자를
나눈 나머지를 반환하는 `mod` 워드도 사용합니다. 이 예에서 맨 위의 숫자는 5이고,
다른 하나는 `buzz?`를 호출하기 전에 스택에 넣은 값입니다. 따라서 `5 mod 0 =`는
스택 맨 위의 값이 5로 나누어떨어지는지 확인하는 불리언 표현식입니다.

    : buzz?  5 mod 0 = if ." Buzz" then ;
    3 buzz?
    4 buzz?
    5 buzz?

{% include editor.html size="small"%}

출력은 다음과 같습니다.

<div class="editor-preview editor-text">3 buzz?<span class="output">  ok</span>
4 buzz?<span class="output">  ok</span>
5 buzz?<span class="output"> Buzz ok</span></div>

`then` 워드가 `if` 문의 끝을 나타낸다는 점을 기억하세요.
예를 들어 Bash의 `fi`나 Ruby의 `end`와 같은 역할을 합니다.

또 하나 기억할 점은 `if`가 참과 거짓을 확인하면서 스택 맨 위의 값을
소비한다는 것입니다.

### `if else then`

`if else then`은 대부분의 언어에서 사용하는 `if/else` 문에 해당합니다.
다음은 사용 예입니다.

    : is-it-zero?  0 = if ." Yes!" else ." No!" then ;
    0 is-it-zero?
    1 is-it-zero?
    2 is-it-zero?

{% include editor.html size="small"%}

출력은 다음과 같습니다.

<div class="editor-preview editor-text">0 is-it-zero?<span class="output"> Yes! ok</span>
1 is-it-zero?<span class="output"> No! ok</span>
2 is-it-zero?<span class="output"> No! ok</span></div>

이번에는 `if`와 `else` 사이의 모든 내용이 참일 때 실행되는 절이고,
`else`와 `then` 사이의 모든 내용이 거짓일 때 실행되는 절입니다.

### `do loop`

Forth의 `do loop`는 C 계열 언어의 `for` 반복문과 가장 비슷합니다.
`do loop`의 본문에서는 특수 워드 `i`가 현재 반복 인덱스를 스택에 넣습니다.

스택 맨 위의 두 값은 `i`의 시작값(포함)과 종료값(제외)을 지정합니다.
시작값은 스택 맨 위에서 가져옵니다. 다음 예제를 보세요.

    : loop-test  10 0 do i . loop ;
    loop-test

{% include editor.html size="small"%}

출력은 다음과 같습니다.

<div class="editor-preview editor-text">loop-test<span class="output"> 0 1 2 3 4 5 6 7 8 9  ok</span></div>

표현식 `10 0 do i . loop`는 대략 다음 코드에 해당합니다.

    for (int i = 0; i < 10; i++) {
      print(i);
    }

### Fizz Buzz

`do loop`를 사용하면 고전적인 [Fizz Buzz](https://en.wikipedia.org/wiki/Fizz_buzz)
프로그램을 쉽게 작성할 수 있습니다.

    : fizz?  3 mod 0 = dup if ." Fizz" then ;
    : buzz?  5 mod 0 = dup if ." Buzz" then ;
    : fizz-buzz?  dup fizz? swap buzz? or invert ;
    : do-fizz-buzz  25 1 do cr i fizz-buzz? if i . then loop ;
    do-fizz-buzz

{% include editor.html %}

`fizz?`는 `3 mod 0
=`를 사용해 스택 맨 위의 값이 3으로 나누어떨어지는지 확인합니다.
그다음 `dup`으로 이 결과를 복제합니다. 맨 위에 있는 복사본은 `if`가 소비하고,
다른 복사본은 스택에 남아 `fizz?`의 반환값 역할을 합니다.

스택 맨 위의 숫자가 3으로 나누어떨어지면 문자열 `"Fizz"`를 출력하고,
그렇지 않으면 아무것도 출력하지 않습니다.

`buzz?`는 5를 기준으로 같은 작업을 수행하며, 문자열 `"Buzz"`를 출력합니다.

`fizz-buzz?`는 `dup`을 호출해 스택 맨 위의 값을 복제한 다음,
`fizz?`를 호출해 맨 위의 복사본을 불리언으로 바꿉니다. 그러면 스택 위쪽에는
원래 값과 `fizz?`가 반환한 불리언이 남습니다. `swap`은 두 값을 맞바꿔
원래 값을 다시 맨 위에 놓고 불리언을 그 아래에 둡니다. 이어서 `buzz?`를
호출하면 맨 위의 값이 불리언 플래그로 바뀝니다. 이제 맨 위의 두 값은
원래 숫자가 각각 3과 5로 나누어떨어지는지를 나타내는 불리언입니다.
그다음 `or`를 호출해 둘 중 하나라도 참인지 확인하고, `invert`로 그 값을 부정합니다.
논리적으로 `fizz-buzz?`의 본문은 다음과 같습니다.

    !(x % 3 == 0 || x % 5 == 0)

따라서 `fizz-buzz?`는 인수가 3으로도 5로도 나누어떨어지지 않아 출력해야 하는지를
나타내는 불리언을 반환합니다. 마지막으로 `do-fizz-buzz`는 1부터 25까지 반복하면서
`i`에 대해 `fizz-buzz?`를 호출하고, `fizz-buzz?`가 참을 반환하면 `i`를 출력합니다.

`fizz-buzz?` 안에서 무슨 일이 일어나는지 이해하기 어렵다면 아래 예제가 도움이 될 것입니다.
여기서는 `fizz-buzz?` 정의의 각 워드를 한 줄씩 따로 실행할 뿐입니다.
각 줄을 실행하면서 스택이 어떻게 변하는지 살펴보세요.

    : fizz?  3 mod 0 = dup if ." Fizz" then ;
    : buzz?  5 mod 0 = dup if ." Buzz" then ;
    4
    dup
    fizz?
    swap
    buzz?
    or
    invert

{% include editor.html %}

각 줄이 스택에 미치는 영향은 다음과 같습니다.

    4         4 <- Top
    dup       4 4 <- Top
    fizz?     4 0 <- Top
    swap      0 4 <- Top
    buzz?     0 0 <- Top
    or        0 <- Top
    invert    -1 <- Top

스택에 마지막으로 남는 값이 `fizz-buzz?` 워드의 반환값이라는 점을 기억하세요.
이 경우에는 숫자가 3으로도 5로도 나누어떨어지지 않으므로 참이며,
따라서 _출력해야_ 합니다.

이번에는 5에서 시작해 같은 과정을 살펴봅시다.

    5         5 <- Top
    dup       5 5 <- Top
    fizz?     5 0 <- Top
    swap      0 5 <- Top
    buzz?     0 -1 <- Top
    or        -1 <- Top
    invert    0 <- Top

이 경우 원래 스택 맨 위에 있던 값이 5로 나누어떨어지므로,
숫자는 출력하지 않아야 합니다.


## 변수와 상수

Forth에서는 변수와 상수에 값을 저장할 수도 있습니다. 변수를 사용하면
변하는 값을 스택에 보관하지 않고도 관리할 수 있습니다.
상수는 변하지 않는 값을 간단히 참조하는 방법을 제공합니다.

### 변수

지역 변수의 역할은 대체로 스택이 담당하므로, Forth의 변수는
여러 워드에서 필요할 수 있는 상태를 저장하는 데 주로 사용합니다.

변수를 정의하는 방법은 간단합니다.

    variable balance

이 코드는 특정 메모리 위치에 `balance`라는 이름을 연결합니다.
이제 `balance`는 워드이며, 자신의 메모리 위치를 스택에 넣는 일만 합니다.

    variable balance
    balance

{% include editor.html size="small"%}

스택에 `1000`이라는 값이 보일 것입니다. 이 Forth 구현에서는 임의로
메모리 위치 `1000`부터 변수를 저장하도록 정했습니다.

`!` 워드는 변수가 가리키는 메모리 위치에 값을 저장하고,
`@` 워드는 메모리 위치에서 값을 가져옵니다.

    variable balance
    123 balance !
    balance @

{% include editor.html size="small"%}

이번에는 스택에 `123`이 보일 것입니다. `123 balance`는 값과 메모리 위치를
스택에 넣고, `!`는 그 메모리 위치에 해당 값을 저장합니다.
마찬가지로 `@`는 메모리 위치에서 값을 가져와 스택에 넣습니다.
C나 C++를 사용해 봤다면 `balance`를 포인터로, `@`를 역참조 연산으로 생각하면 됩니다.

`?` 워드는 `@ .`로 정의되어 있으며 변수의 현재 값을 출력합니다.
`+!` 워드는 변수의 값을 지정한 양만큼 증가시킵니다.
C 계열 언어의 `+=`와 비슷합니다.

    variable balance
    123 balance !
    balance ?
    50 balance +!
    balance ?

{% include editor.html size="small"%}

이 코드를 실행하면 다음과 같은 결과가 나타납니다.

<div class="editor-preview editor-text">variable balance<span class="output">  ok</span>
123 balance ! <span class="output"> ok</span>
balance ? <span class="output">123  ok</span>
50 balance +! <span class="output"> ok</span>
balance ? <span class="output">173  ok</span>
</div>

### 상수

변하지 않는 값은 상수로 저장할 수 있습니다. 상수는 다음처럼 한 줄로 정의합니다.

    42 constant answer

이 코드는 값이 `42`인 새 상수 `answer`를 만듭니다. 변수와 달리
상수는 메모리 위치가 아니라 값 자체를 나타내므로 `@`를 사용할 필요가 없습니다.

    42 constant answer
    2 answer *

{% include editor.html size="small"%}

이 코드를 실행하면 스택에 `84`가 들어갑니다. `answer`는 자신이 나타내는
숫자처럼 취급됩니다. 다른 언어의 상수나 변수와 마찬가지입니다.


## 배열

Forth가 배열을 직접 지원하는 것은 아니지만, C의 배열처럼 연속된 메모리 영역을
할당할 수는 있습니다. 메모리를 할당할 때는 `allot` 워드를 사용합니다.

    variable numbers
    3 cells allot
    10 numbers 0 cells + !
    20 numbers 1 cells + !
    30 numbers 2 cells + !
    40 numbers 3 cells + !

{% include editor.html size="small"%}

이 예제는 `numbers`라는 메모리 위치를 만들고, 그 뒤에 셀 세 개를 추가로 확보해
총 네 개의 메모리 셀을 마련합니다. (`cells`는 셀의 크기를 곱할 뿐이며,
이 구현에서 셀의 크기는 1입니다.)

`numbers 0 +`는 배열의 첫 번째 셀 주소를 구합니다.
`10 numbers 0 + !`는 배열의 첫 번째 셀에 값 `10`을 저장합니다.

배열 접근을 간단하게 만드는 워드도 쉽게 작성할 수 있습니다.

    variable numbers
    3 cells allot
    : number  ( offset -- addr )  cells numbers + ;

    10 0 number !
    20 1 number !
    30 2 number !
    40 3 number !

    2 number ?

{% include editor.html size="small"%}

`number`는 `numbers` 안의 오프셋을 받아 그 위치의 메모리 주소를 반환합니다.
`30 2 number !`는 `numbers`의 오프셋 `2`에 `30`을 저장하고,
`2 number ?`는 `numbers`의 오프셋 `2`에 있는 값을 출력합니다.


## 키보드 입력

Forth에는 키보드 입력을 받는 특수 워드인 `key`가 있습니다.
`key` 워드를 실행하면 키를 누를 때까지 실행이 멈춥니다.
키를 누르면 해당 키의 키 코드가 스택에 들어갑니다. 다음을 실행해 보세요.

    key . key . key .

{% include editor.html size="small"%}

이 줄을 실행하면 처음에는 아무 일도 일어나지 않는 것처럼 보입니다.
인터프리터가 키보드 입력을 기다리고 있기 때문입니다. `A` 키를 누르면
현재 줄의 출력에 그 키의 코드인 `65`가 나타납니다.
이어서 `B`, `C`를 누르면 다음과 같은 결과가 나옵니다.

<div class="editor-preview editor-text">key . key . key . <span class="output">65 66 67  ok</span></div>


### `begin until`로 키 출력하기

Forth에는 `begin until`이라는 다른 종류의 반복문도 있습니다.
이 반복문은 C 계열 언어의 `while` 반복문처럼 동작합니다.
인터프리터는 `until` 워드를 만날 때마다 스택 맨 위의 값이 0이 아닌지(참인지) 확인합니다.
참이면 대응하는 `begin`으로 돌아가고, 그렇지 않으면 다음 실행을 이어 갑니다.

다음은 `begin until`을 사용해 키 코드를 출력하는 예제입니다.

    : print-keycode  begin key dup . 32 = until ;
    print-keycode

{% include editor.html size="small"%}

스페이스 키를 누를 때까지 키 코드를 계속 출력합니다. 다음과 같은 결과가 나타납니다.

<div class="editor-preview editor-text">print-keycode <span class="output">80 82 73 78 84 189 75 69 89 67 79 68 69 32  ok</span></div>

`key`가 키 입력을 기다린 다음, `dup`이 `key`에서 받은 키 코드를 복제합니다.
이어서 `.`으로 맨 위의 키 코드 복사본을 출력하고, `32 =`로 키 코드가 32인지 확인합니다.
같으면 반복문을 빠져나오고, 그렇지 않으면 `begin`으로 돌아가 반복합니다.


## 뱀 게임!

이제 배운 내용을 모두 모아 게임을 만들 차례입니다!
코드를 전부 입력할 필요가 없도록 편집기에 미리 넣어 두었습니다.

코드를 살펴보기 전에 먼저 게임을 해 보세요. `start` 워드를 실행하면 게임이 시작됩니다.
화살표 키로 뱀을 움직이세요. 게임이 끝나면 `start`를 다시 실행할 수 있습니다.

{% include editor.html canvas=true game=true %}

코드를 자세히 살펴보기 전에 두 가지를 말씀드리겠습니다. 첫째, 이 코드는 잘 작성된
Forth 코드가 아닙니다. 저는 Forth 전문가가 아니므로 여러 부분을 잘못된 방식으로
작성했을 수도 있습니다. 둘째, 이 게임은 JavaScript와 연동하기 위해
몇 가지 비표준 기법을 사용합니다. 지금부터 하나씩 살펴보겠습니다.

### 비표준 확장 기능

#### 캔버스

이 편집기는 다른 편집기와 달리 HTML5 캔버스 요소가 내장되어 있습니다.
이 캔버스에 그림을 그리기 위해 아주 간단한 메모리 매핑 인터페이스를 만들었습니다.
캔버스는 가로 24개, 세로 24개의 "픽셀"로 나뉘며 각 픽셀은 검은색 또는 흰색입니다.
첫 번째 픽셀은 `graphics` 변수가 가리키는 메모리 주소에 있고,
나머지 픽셀은 그 주소에서의 오프셋으로 접근합니다. 예를 들어 왼쪽 위 모서리에
흰색 픽셀을 그리려면 다음을 실행하면 됩니다.

    1 graphics !

{% include editor.html size="small" canvas=true %}

게임에서는 다음 워드로 캔버스에 그림을 그립니다.

    : convert-x-y ( x y -- offset )  24 cells * + ;
    : draw ( color x y -- )  convert-x-y graphics + ! ;
    : draw-white ( x y -- )  1 rot rot draw ;
    : draw-black ( x y -- )  0 rot rot draw ;

예를 들어 `3 4 draw-white`는 좌표 (3, 4)에 흰색 픽셀을 그립니다.
y 좌표에 24를 곱해 행의 위치를 구한 다음, x 좌표를 더해 열의 위치를 구합니다.

#### 실행을 막지 않는 키보드 입력

Forth의 `key` 워드는 입력을 기다리는 동안 실행을 멈추므로 이런 게임에는 적합하지 않습니다.
그래서 가장 최근에 누른 키의 값을 항상 보관하는 `last-key` 변수를 추가했습니다.
`last-key`는 인터프리터가 Forth 코드를 실행하는 동안에만 갱신됩니다.

#### 난수 생성

Forth 표준에는 난수를 생성하는 방법이 정의되어 있지 않으므로,
범위를 받아 0부터 range - 1까지의 난수를 반환하는 `random ( range -- n )` 워드를 추가했습니다.
예를 들어 `3 random`은 `0`, `1`, `2` 중 하나를 반환합니다.

#### `sleep ( ms -- )`

마지막으로 지정한 밀리초만큼 실행을 일시 중지하는 `sleep` 워드를 추가했습니다.

### 게임 코드

이제 코드를 처음부터 끝까지 살펴봅시다.

#### 변수와 상수

코드의 시작 부분에서는 변수와 상수를 준비합니다.

    variable snake-x-head
    500 cells allot

    variable snake-y-head
    500 cells allot

    variable apple-x
    variable apple-y

    0 constant left
    1 constant up
    2 constant right
    3 constant down

    24 constant width
    24 constant height

    variable direction
    variable length

`snake-x-head`와 `snake-y-head`는 뱀 머리의 x 좌표와 y 좌표를 저장하는 메모리 위치입니다.
각 위치 뒤에는 뱀 꼬리의 좌표를 저장하기 위해 메모리 셀 500개를 할당합니다.

다음으로 뱀의 몸통을 나타내는 메모리 위치에 접근할 두 워드를 정의합니다.

    : snake-x ( offset -- address )
      cells snake-x-head + ;

    : snake-y ( offset -- address )
      cells snake-y-head + ;

앞에서 살펴본 `number` 워드처럼, 이 두 워드는 뱀의 각 마디를 저장한 배열의 요소에
접근하는 데 사용합니다. 그다음에는 앞에서 설명한 캔버스 그리기 워드들이 나옵니다.

네 방향은 상수(`left`, `up`, `right`, `down`)로 나타내고,
현재 방향은 `direction` 변수에 저장합니다.

#### 초기화

이어서 모든 상태를 초기화합니다.

    : draw-walls
      width 0 do
        i 0 draw-black
        i height 1 - draw-black
      loop
      height 0 do
        0 i draw-black
        width 1 - i draw-black
      loop ;

    : initialize-snake
      4 length !
      length @ 1 + 0 do
        12 i - i snake-x !
        12 i snake-y !
      loop
      right direction ! ;

    : set-apple-position apple-x ! apple-y ! ;

    : initialize-apple  4 4 set-apple-position ;

    : initialize
      width 0 do
        height 0 do
          j i draw-white
        loop
      loop
      draw-walls
      initialize-snake
      initialize-apple ;

`draw-walls`는 두 개의 `do/loop`를 사용해 가로 벽과 세로 벽을 각각 그립니다.

`initialize-snake`는 `length` 변수를 `4`로 설정한 뒤, `0`부터 `length + 1`까지
반복하면서 뱀의 초기 위치를 채웁니다. 뱀을 쉽게 늘릴 수 있도록
위치 정보는 항상 실제 길이보다 하나 더 저장합니다.

`set-apple-position`과 `initialize-apple`은 사과의 초기 위치를 (4,4)로 설정합니다.

마지막으로 `initialize`가 화면 전체를 흰색으로 채우고 세 초기화 워드를 호출합니다.

#### 뱀 움직이기

현재 `direction` 값에 따라 뱀을 움직이는 코드는 다음과 같습니다.

    : move-up  -1 snake-y-head +! ;
    : move-left  -1 snake-x-head +! ;
    : move-down  1 snake-y-head +! ;
    : move-right  1 snake-x-head +! ;

    : move-snake-head  direction @
      left over  = if move-left else
      up over    = if move-up else
      right over = if move-right else
      down over  = if move-down
      then then then then drop ;

    \ Move each segment of the snake forward by one
    : move-snake-tail  0 length @ do
        i snake-x @ i 1 + snake-x !
        i snake-y @ i 1 + snake-y !
      -1 +loop ;

`move-up`, `move-left`, `move-down`, `move-right`는 뱀 머리의 x 좌표나 y 좌표에
1을 더하거나 뺍니다. `move-snake-head`는 `direction` 값을 확인하고
알맞은 `move-*` 워드를 호출합니다. `over = if` 패턴은 Forth에서
다중 분기문을 구현할 때 흔히 사용하는 방식입니다.

`move-snake-tail`은 뱀의 위치 배열을 뒤에서부터 훑으면서 각 값을 인덱스가 1 큰 셀에
복사합니다. 뱀 머리를 움직이기 전에 호출하여 몸의 각 마디를 한 칸씩 앞으로 옮깁니다.
여기서는 `do/loop`의 변형인 `do/+loop`를 사용합니다. 이 반복문은 인덱스를 매번 1씩
증가시키는 대신, 반복할 때마다 스택에서 값을 꺼내 인덱스에 더합니다.
따라서 `0 length @
do -1 +loop`는 `length`부터 `0`까지 `-1`씩 변화하며 반복합니다.

#### 키보드 입력

다음 부분은 키보드 입력을 받아, 필요한 경우 뱀의 방향을 바꾸는 코드입니다.

    : is-horizontal  direction @ dup
      left = swap
      right = or ;

    : is-vertical  direction @ dup
      up = swap
      down = or ;

    : turn-up     is-horizontal if up direction ! then ;
    : turn-left   is-vertical if left direction ! then ;
    : turn-down   is-horizontal if down direction ! then ;
    : turn-right  is-vertical if right direction ! then ;

    : change-direction ( key -- )
      37 over = if turn-left else
      38 over = if turn-up else
      39 over = if turn-right else
      40 over = if turn-down
      then then then then drop ;

    : check-input
      last-key @ change-direction
      0 last-key ! ;

`is-horizontal`과 `is-vertical`은 `direction` 변수의 현재 값이
가로 방향인지 세로 방향인지 확인합니다.

`turn-*` 워드는 새 방향을 설정합니다. 다만 먼저 `is-horizontal`과 `is-vertical`로
현재 방향을 확인하여 새 방향이 유효한지 판단합니다. 예를 들어 뱀이 가로로 움직이는 중이라면
새 방향을 `left`나 `right`로 설정하는 것은 적절하지 않습니다.

`change-direction`은 키를 받아 화살표 키라면 알맞은 `turn-*` 워드를 호출합니다.
`check-input`은 `last-key`라는 의사 변수에서 마지막 키를 가져와
`change-direction`을 호출한 다음, 가장 최근 입력을 처리했다는 뜻으로
`last-key`를 0으로 설정합니다.

#### 사과

다음 코드는 뱀이 사과를 먹었는지 확인하고, 먹었다면 사과를 새로운 무작위 위치로 옮깁니다.
사과를 먹었을 때는 뱀의 길이도 늘립니다.

    \ get random x or y position within playable area
    : random-position ( -- pos )
      width 4 - random 2 + ;

    : move-apple
      apple-x @ apple-y @ draw-white
      random-position random-position
      set-apple-position ;

    : grow-snake  1 length +! ;

    : check-apple ( -- flag )
      snake-x-head @ apple-x @ =
      snake-y-head @ apple-y @ =
      and if
        move-apple
        grow-snake
      then ;

`random-position`은 `2`부터 `width - 2` 범위의 무작위 x 좌표 또는 y 좌표를 생성합니다.
이렇게 하면 사과가 벽 바로 옆에 나타나는 일을 막을 수 있습니다.

`move-apple`은 `draw-white`로 현재 사과를 지우고, `random-position`을 두 번 사용해
사과의 새 x/y 좌표를 만듭니다. 마지막으로 `set-apple-position`을 호출해
사과를 새 좌표로 옮깁니다.

`grow-snake`는 `length` 변수에 1을 더하기만 합니다.

`check-apple`은 사과와 뱀 머리의 x/y 좌표가 같은지 비교합니다.
`=`를 두 번 사용하고 `and`로 두 불리언을 결합합니다.
좌표가 같으면 `move-apple`을 호출해 사과를 새 위치로 옮기고,
`grow-snake`를 호출해 뱀의 길이를 한 마디 늘립니다.

#### 충돌 감지

다음으로 뱀이 벽이나 자기 몸에 부딪혔는지 확인합니다.

    : check-collision ( -- flag )
      \ get current x/y position
      snake-x-head @ snake-y-head @

      \ get color at current position
      convert-x-y graphics + @

      \ leave boolean flag on stack
      0 = ;

`check-collision`은 뱀 머리의 새 위치가 이미 검은색인지 확인합니다.
이 워드는 뱀의 위치를 갱신한 _뒤_, 새 위치에 뱀을 그리기 _전_에 호출합니다.
충돌이 발생했는지를 나타내는 불리언을 스택에 남깁니다.

#### 뱀과 사과 그리기

다음 두 워드는 뱀과 사과를 그리는 역할을 합니다.

    : draw-snake
      length @ 0 do
        i snake-x @ i snake-y @ draw-black
      loop
      length @ snake-x @
      length @ snake-y @
      draw-white ;

    : draw-apple
      apple-x @ apple-y @ draw-black ;

`draw-snake`는 뱀 배열의 각 셀을 순회하며 검은색 픽셀을 하나씩 그립니다.
그런 다음 오프셋 `length`에 해당하는 위치에 흰색 픽셀을 그립니다.
꼬리의 마지막 마디는 배열의 `length - 1` 위치에 있으므로,
`length`에는 이동 전 마지막 꼬리 마디의 위치가 들어 있습니다.

`draw-apple`은 사과의 현재 위치에 검은색 픽셀을 그리기만 합니다.

#### 게임 루프

게임 루프는 충돌이 발생할 때까지 계속 반복하면서 앞에서 정의한 워드들을 차례로 호출합니다.

    : game-loop ( -- )
      begin
        draw-snake
        draw-apple
        100 sleep
        check-input
        move-snake-tail
        move-snake-head
        check-apple
        check-collision
      until
      ." Game Over" ;

    : start  initialize game-loop ;

`begin/until` 반복문은 `check-collision`이 반환한 불리언으로 반복을 계속할지
종료할지 결정합니다. 반복문을 빠져나오면 `"Game Over"` 문자열을 출력합니다.
매 반복마다 `100 sleep`으로 100밀리초 동안 멈추므로,
게임은 초당 약 10프레임으로 실행됩니다.

`start`는 `initialize`를 호출해 모든 상태를 초기화한 다음 `game-loop`를 시작합니다.
모든 초기화가 `initialize` 워드에서 이루어지므로,
게임이 끝난 뒤에도 `start`를 다시 호출할 수 있습니다.

------

이것으로 끝입니다! 게임의 모든 코드를 이해하는 데 도움이 되었기를 바랍니다.
이해가 잘 안 되는 부분이 있다면 워드를 하나씩 실행하면서 스택이나 변수에
어떤 영향을 주는지 확인해 보세요.


## 마치며

Forth는 여기서 설명한 내용이나 제가 인터프리터에 구현한 기능보다 훨씬 강력합니다.
실제 Forth 시스템에서는 컴파일러의 동작 방식을 바꾸거나 새로운 정의용 워드를
만들 수 있습니다. 이를 통해 환경을 자유롭게 맞춤 설정하고
Forth 안에서 자신만의 언어를 만들 수도 있습니다.

Forth의 강력한 기능을 더 배우고 싶다면 Leo Brodie의 짧은 책
["Starting Forth"](http://www.forth.com/starting-forth/)가 좋은 자료입니다.
온라인에서 무료로 읽을 수 있으며, 여기서 다루지 않은 흥미로운 내용을 배울 수 있습니다.
배운 내용을 점검할 수 있는 좋은 연습 문제도 들어 있습니다.
다만 코드를 실행하려면 [SwiftForth](http://www.forth.com/swiftforth/dl.html)를
내려받아야 합니다.
