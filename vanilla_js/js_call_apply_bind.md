# call, apply, bind

### call, apply란

call, apply, bind는 함수의 기본 메서드입니다.

보통 함수 뒤에 ()를 붙여서 호출하지만 call, apply를 이용해 호출하는 방법도 있습니다.

call은 보통 함수와 똑같이 인자를 넣고 apply는 배열로 넣는 것을 알 수 있습니다.

```jsx
var sum = function (a, b, c) {
  return a + b + c;
};

sum(1, 2, 3);
sum.call(null, 1, 2, 3);
sum.apply(null, [1, 2, 3]);
```

일반적인 방법과 call, apply의 차이점은 this를 대체하는 것입니다. 

```jsx
var obj = {
  string: 'zero',
  yell: function() {
    alert(this.string);
  }
};
var obj2 = {
  string: 'what?'
};
obj.yell(); // 'zero';
obj.yell.call(obj2); // 'what?'
```

obj.yell.call(obj2)로 this가 가리키는 것을 obj에서 obj2로 바꾸었습니다.

다른 객체의 함수를 자기 것처럼 사용할 수 있습니다.

this를 바꿀 수 있는 방법 중 하나입니다.

### call, apply 활용

예를 들어 함수의 argument를 조작할때 사용합니다.

arguments는 유사 배열이기 때문에 join같은 배열의 메서드를 사용할 수 없습니다.

call을 활용하면 이 문제를 해결할 수 있습니다.

```jsx
function example3() {
  console.log(Array.prototype.join.call(arguments));
}
example3(1, 'string', true); // '1,string,true'
```

ES5에서 상속을 구현할때도 사용할 수 있다.

```jsx
function Vehicle(name, speed) {
  this.name = name;
  this.speed = speed;
}

function Sedan(name, speed, maxSpeed) {
  Vehicle.apply(this, arguments);
  this.maxSpeed = maxSpeed;
}

var tico = new Vehicle('tico', 50);
var sonata = new Sedan('sonata', 100, 200);

console.log(tico);   // name: "tico", speed: 50
console.log(sonata); // maxSpeed: 200, name: "sonata", speed: 100
```

### bind

bind는 this는 바꾸되 호출하지 않는 것입니다.

call(this, 1, 2, 3)은 bind(this)(1, 2, 3)과 같습니다.

```jsx
var obj = {
  string: 'zero',
  yell: function() {
    alert(this.string);
  }
};
var obj2 = {
  string: 'what?'
};
obj.yell(); // 'zero';
var yell2 = obj.yell.bind(obj2);
yell2(); //what?

```

### 마무리

이 내용은 아래 참조 사이트의 내용을 정리한 것입니다.

더 자세한 내용을 원하시면 참조 사이트를 읽어 보시는 것을 추천드립니다.

zerocho님 강의- [https://www.zerocho.com/category/JavaScript/post/57433645a48729787807c3fd](https://www.zerocho.com/category/JavaScript/post/57433645a48729787807c3fd)