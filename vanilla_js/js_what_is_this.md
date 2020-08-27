# javascript에서의 this란

자바스크립트에 대해서 조금 더 배우다보면 내가 알고 있다고 생각했던 this가 헷갈리기 시작한다.

아래 사이트의 내용을 정리한 글입니다https://www.zerocho.com/category/JavaScript/post/5b0645cc7e3e36001bf676eb

### 기본으로 가리키는 것은 window다.

크롬 개발자 모드에서 this를 출력해보면 window가 나온다.

기본으로 가리키는 것은 this다.

### 객체의 메서드에서 this는 객체를 가리킨다.

```jsx
var obj = {
  a: "Hi!",
  b: function() { console.log(this.a); }
};
obj.b();  // Hi!
```

### call, apply, bind를 이용해 this를 바꿀 수 있다.

call, apply 사용

```jsx
var sum = function (a, b, c) {
  return a + b + c;
};

sum(1, 2, 3);
sum.call(null, 1, 2, 3);
sum.apply(null, [1, 2, 3]);
```

bind 사용

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

### 🐧 여기까지는 아는 내용이지만 이벤트를 만나게 되면 헷갈리기 시작한다.

특히 리엑트에서..

**이벤트가 발생할때 내부적으로 this가 바뀐다.**

```jsx
document.body.onclick = function() {
  console.log(this); // <body>
}
```

본격 헷갈리는 응용 사례다.

클릭 이벤트 바깥에서는 this가 <div>인 것은 이해가 되지만 inner 안에서 this가 window인것은 이해가 되지않는다.

그저 click 이벤트 리스너가 잘못한 것이라고 이해해야겠다.

아래와 같은 상황이라면 inner의 this가 window이므로 원하는대로 흘러가지 않게된다.

```jsx
$('div').on('click', function() {
  console.log(this); // <div>
  function inner() {
    console.log('inner', this); // inner Window
  }
  inner();
});
```

**이를 해결하려면 this를 특정 변수에 넣어서 사용하거나, ES6 화살표 함수를 사용해야한다.**

화살표 함수는 this로 window 대신 상위 this를 가져오게 된다.

```jsx
$('div').on('click', function() {
  console.log(this); // <div>
  const inner = () => {
    console.log('inner', this); // inner <div>
  }
  inner();
});
```

**리엑트에서 사용할때에도 똑같은 문제가 발생한다**.

이벤트를 따로 바인드 하지않으면 onClickButton의 this는 inner처럼 window나 undefined를 가리킨다.

```jsx
class Basic extends Component {
  constructor(props) {
    super(props);
    this.state = {
      hidden: false,
    };
    this.onClickButton = this.onClickButton.bind(this);
  }

  onClickButton() {
    this.setState(() => ({ hidden: true }));
  }

  render() {
    return (
      <div>
        <span>저는 {this.props.lang} 전문 {this.props.name}입니다!</span>
        {!this.state.hidden && <span>{this.props.birth}년에 태어났습니다.</span>}
        <button onClick={this.onClickButton}>숨기기</button>
      </div>
    );
  }
}
```

지금 onClickButton의 this는 Click 이벤트 리스너의 잘못으로 window를 가리키고 있다.

어떻게하면 onClickButton의 this가 class를 바라보게 할 수 있을까?

### 🌟 onClickButton를 bind 하거나 화살표 함수를 사용하면 된다!

먼저 onClickButton를 constructor에서 bind하게되면 onClickButton의 this는 class를 바라보게 된다.

```jsx
this.onClickButton = this.onClickButton.bind(this);
```

onClickButton를 화살표 함수로 사용하게 되면 this는 자연스럽게 상위 this를 사용하게 된다.

```jsx
onClickButton = () => {
	this.setState(() => ({ hidden: true }));
}
```