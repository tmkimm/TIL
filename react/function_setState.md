# 함수형 setState

일반적으로 setState시 객체를 return한다

```jsx
this.setState({
	value: this.state.value + 1
})
```

하지만 setState 시 현재 state에 접근하려면 비동기로 처리되기 때문에 시점에 따라 값이 달라질 수 있다.

이럴때 함수형 setState를 사용한다.

```jsx
this.setState((prevState) => {
	return {
		value: prevState + 1
	}
})
```

아래와 같이 선언적으로도 사용할 수 있다!

```jsx
// outside your component class
function increaseScore (state, props) {
  return {score : state.score + 1}
}
class User{
  ...
// inside your component class
  handleIncreaseScore () {
    this.setState( increaseScore)
  }
  ...
}
```

모듈화해서 사용도 가능하다.

```
import {increaseScore} from "../stateChanges";
class User{
  ...
  // inside your component class
  handleIncreaseScore () {
    this.setState( increaseScore)
  }
  ...
}
```