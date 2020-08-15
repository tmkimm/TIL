# 렉시컬 스코핑, 실행 컨텍스트

### 들어가기에 앞서

이 내용은 아래 사이트의 내용을 정리한 것입니다.

더 자세한 내용을 원하시면 참조 사이트를 읽어 보시는 것을 추천드립니다.

zerocho님 강의- [https://www.zerocho.com/category/JavaScript/post/57433645a48729787807c3fd](https://www.zerocho.com/category/JavaScript/post/5741d96d094da4986bc950a0)

poiemaweb 스코프 - https://poiemaweb.com/js-scope

프로토 타입과 아래 내용들을 정리하고 나니 Javascript에 대한 이해도가 확 높아진 기분이다.

아래의 내용을 이해하려면 먼저 렉시컬 스코핑 개념을 알고있어야한다.

### 렉시컬 스코핑(lexical scoping)이란

다른 말로는 정적 스코프(Static scope)라고 한다. 의미는 어렵지 않다.

아래와 같은 상황에서 두가지 패턴을 예측할 수 있는데

1. 함수를 어디서 호출하였는지에 따라 상위 스코프를 결정
 - 이 방식이라면 wow의 상위 스코프는 say
2. 함수를 어디서 선언하였는지에 따라 상위 스코프를 결정
 - 이 방식이라면 wow의 상위 스코프는 전역

```jsx
var name = 'zero';
function wow(word) {
  console.log(word + ' ' + name);
}
function say () {
  var name = 'nero';
  console.log(name);
  wow('hello');
}
say(); // ?
```

첫번째 방식이 동적 스코프(Dynamic scope), 두번째 방식이 **렉시컬 스코프(Lexical scope)** 또는 정적 스코프(Static scope)라고 한다.

Javascript를 비롯한 대부분의 프로그래밍 언어는 **렉시컬 스코프**를 따른다.

따라서 함수 wow의 상위 스코프는 전역 스코프고 nero, hello zero가 출력된다.

## ⭐ 실행 컨텍스트

실행 컨텍스트를 이해하면 자바스크립트가 어떻게 동작하는지 이해할 수 있다.

ECMAScript 스펙에 따르면 실행 컨텍스트를 실행 가능한 코드를 형상화하고 구분하는 추상적인 개념이라고 정의한다.

좀 더 쉽게 말하자면 실행 컨텍스트는 실행 가능한 코드가 실행되기 위해 필요한 환경 이라고 말할 수 있겠다.

1. 처음 코드가 실행하는 순간 모든 것을 관리하는 환경인 전역 컨텍스트가 생성된다.
페이지가 종료될 때 까지 유지된다.
2. 이후 함수 호출시마다 함수 컨텍스트가 생성된다.
모든 컨텍스트는 변수 객체, scope chain, this를 가지고있다.
3. 함수 컨텍스트 생성 후 함수가 실행되는데 사용되는 변수들은 변수 객체 안에서 값을 찾고 없으면 scope chain을 따라 올라가며 찾는다.
4. 함수 실행이 끝나면 해당 컨텍스트는 사라진다.(클로저 제외)
페이지가 종료되면 전역 컨텍스트가 사라진다.

**변수 객체**란 해당 스코프의 변수들을 말한다.

전역 컨텍스트의 변수 객체는 name, wow, say가 되겠다.

say에서는 name

wow에서는 word(매개변수)다.

scope chain이란 객체를 찾을 수 없을 경우 상위 변수 객체에서 찾는 것을 말한다.

Prototype chain과 동일한 동작이다.

```jsx
var name = 'zero';
function wow(word) {
  console.log(word + ' ' + name);
}
function say () {
  var name = 'nero';
  console.log(name);
  wow('hello');
}
say();
```

### 🌟 예제

아래의 예제를 보면 

1. 전역 컨텍스트가 생성되고
(전역 컨텍스트의 변수 객체 : name, wow, say)
2. say(); 하는 순간 say 함수 컨텍스트가 생성된다.
(say 컨텍스트의 변수 객체 : name  /  scope chain : say 변수 객체와 전역 변수 객체)
3. say 변수 객체에 name이 있으므로 nero를 출력한다.
wow('hello');를 호출해야 하지만 say변수 객체에 wow 변수가 없으므로 scope chain을 통해 전역 변수 객체에서 찾아 호출한다.
4. 2번과 마찬가지로 wow 함수 컨텍스트가 생성되고 실행 후 종료된다.

여기서 wow 의 상위 객체는 lexical scoping때문에 전역 객체가 된다.

```jsx
var name = 'zero';
function wow(word) {
  console.log(word + ' ' + name);
}
function say () {
  var name = 'nero';
  console.log(name);
  wow('hello');
}
say();
```