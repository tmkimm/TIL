# React 상태 객체

### 상태 객체가 없다면 React 컴포넌트는 정적 템플릿일 뿐이다.

---

속성(props)은 해당 컴포넌트 생성 시에 전달받는 값이기 때문에 내부에서 수정할 수 없다.

다시말해 속성은 현재 컴포넌트에서 변경할 수 없으므로 부모 컴포넌트에서 컴포넌트를 새로 생성해서 새로운 값을 전달 하는 방법 외에는 컴포넌트의 속성을 변경할 수 없다.

상태 객체를 이용하면 이런 문제를 해결할 수 있다.

### 초기 상태 설정하기

초기 상태 설정하려면 클래스의 생성자에서 this.state를 선언한다.

```jsx
class Clock extends React.Component {
    constructor(props) {
        super(props)
        this.state = {currentTime: (new Date()).toLocaleString('en')}
    }
}
```

construcor() 메서드는 React 엘리먼트가 생성되는 시점에 한번만 호출된다.

이렇게 하면 첫 번째 값을 입력해서 보여줄 뿐 곧 시간이 지나버릴 것이다.

### 상태 갱신하기

this.setState(data, callback)를 사용하면 상태를 변경할 수 있다.

이 메서드를 실행하면 React는 전달하는 data를 현재 상태에 **병합**하고 render()를 호출한다.

이 후에 React가 callback 함수를 실행한다.

setState()로 전달하는 상태는 상태 객체의 일부분만 갱신한다.(나머지는 갱신하지 않는다)

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props)
    this.launchClock()  // 1초마다 갱신
    this.state = {currentTime: (new Date()).toLocaleString()}  // 상태 객체 초기화
  }
  launchClock() {
    setInterval(()=> {
      console.log('Updating time...')
      this.setState({
        currentTime: (new Date()).toLocaleString()
      })
    }, 1000)
  }
  render() {
    console.log('Rendering Clock...')
    return <div>{this.state.currentTime}</div>
  }
}
```

### 상태와 속성

상태와 속석은 모두 클래스의 멤버이며, 각각 this.state와 this.props를 말한다. 

주요한 차이점 중 하나는 상태는 변경이 가능하지만 속성은 변경이 불가능하다는 점이다.

속성은 부모 컴포넌트에서 전달하지만 상태는 해당 컴포넌트 자체에서 정의한다.

이는 속성 값을 변경하는 것은 오직 부모 컴포넌트만 가능하고 자체적으로 변경할 수 없다.

그러므로 속성은 뷰 생성 시 정해지고, 정적인 상태로 유지된다.(변경되지 않는다)

반면 상태는 해당 컴포넌트에서 설정되고 갱신된다.

모든 컴포넌트가 상태를 가져야 하는것은 아니다.

### 상태 비저장 컴포넌트, 상태 저장 컴포넌트

상태 비저장 컴포넌트는 상태 객체가 없으며 컴포넌트 메서드 또는 다른 React의 라이클 이벤트 또는 메서드를 갖지 않는다. 

오직 뷰를 렌더링 하는것이다. 이 컴포넌트가 할 수 있는 것은 속성을 전달받아 처리하는 것 뿐이다.

더 선언적이고 더 나은 문법을 바팡으로 좀 더 간결하게 컴포넌트를 작성할 수 있기때문에 상태 비저장 컴포넌트를 가능한 많이 사용하는 것이 좋다.(React 팀이 소개하는 모범사례)!

하지만 항상 상태 비저장 컴포넌트를 사용할 수 있는 것이 아니기때문에

상태 저장 컴포넌트와 적절히 사용하는 것이 좋다. 좀 더 유연하고, 간단하며, 더 나은 설계를 할 수 있도록.

Ex) 시계 컴포넌트(상태저장) 안에 비저장 컴포넌트를 속성으로 전달

### 상태 비저장 컴포넌트 좋은 패턴

보통 함수나 화살표 함수 문법으로 작성한 컴포넌트를 의미한다.

상태 비저장 컴포넌트도 메서드를 추가할 수 있다.

**안티패턴**

```jsx
const DigitalDisplay = function(props) {
  return <div>{DigitalDisplay.locale(props.time)}</div>
}
DigitalDisplay.locale = (time) => {
  return (new Date(time)).toLocaleString('EU')
}
```

**좋은 패턴**

```jsx
const DigitalDisplay = function(props) {
  const locale = time => (new Date(time)).toLocaleString('EU')
  return <div>{DigitalDisplay.locale(props.time)}</div>
}
```