# React에서 폼 다루기

### React는 사용자가 값을 바꿀 수 없게한다.(단방향 바인딩)

이상해보이지만 React 입장에서는 올바른 동작이다.

그렇지만 입력 영역은 사용자 입력이나 클릭에 따라 변경되어야 한다.

상태로 값을 관리하고 React가 변경을 감지할 수 있도록 onChange에 이벤트 핸들러를 추가해야 한다.

```jsx
handleChange(event){
	this.setState({title: event.target.value})
}
render() {
  return <input type="text" name="title" value={this.state.title}
          onChange={this.handleChange.bind(this)} />
}
```

### form 다루기

<form>으로 감싸는 것이 필수적인 것은 아니지만, 현명한 방법이다.

form은 3가지 이벤트를 지원한다.

- onChange : 폼의 입력 요소에 변경이 생기면 발생한다.
- onSubmit : 폼 제출 시 발생한다. 흔히 enter를 눌렀을때.
- onInput: 값이 변경될때마다 발생한다.

enter를 눌렀을때 폼을 제출하도록 하려면 onKeyUp이벤트로 처리하면 된다.

### 여러개의 input 받을때

[e.target.name]: e.target.value 이런 식으로 사용하면 변수 값을 key로 사용할 수 있다.

```jsx
import React, { Component } from 'react';

class PhoneForm extends Component {
  state = {
    name: '',
    phone: ''
  }
  handleChange = (e) => {
    this.setState({
      [e.target.name]: e.target.value
    });
  }
  render() {
    return (
      <form>
        <input
          placeholder="이름"
          value={this.state.name}
          onChange={this.handleChange}
          name="name"
        />
        <input
          placeholder="전화번호"
          value={this.state.phone}
          onChange={this.handleChange}
          name="phone"
        />
        <div>{this.state.name} {this.state.phone}</div>
      </form>
    );
  }
}

export default PhoneForm;
```

### 부모 컴포넌트에서 하위 컴포넌트 값 접근하기

부모 컴포넌트에서 메서드를 만든후 props에 메서드를 전달해주면 하위 컴포넌트에서 해당 메서드에 접근할 수 있다.

예를 들어 PhoneForm 컴포넌트에서 입력한 값을 App에서 접근하고 싶을 경우

app.jsx

```jsx
class App extends Component {
  handleCreate = (data) => {
    console.log(data);
  }
  render() {
    return (
      <div>
        <PhoneForm
          onCreate={this.handleCreate}
        />
      </div>
    );
  }
}
```

phoneform.jsx

```jsx
// file: src/components/PhoneForm.js
import React, { Component } from 'react';

class PhoneForm extends Component {
  state = {
    name: '',
    phone: ''
  }
  handleChange = (e) => {
    this.setState({
      [e.target.name]: e.target.value
    })
  }
  handleSubmit = (e) => {
    // 페이지 리로딩 방지
    e.preventDefault();
    // 상태값을 onCreate 를 통하여 부모에게 전달
    this.props.onCreate(this.state);
    // 상태 초기화
    this.setState({
      name: '',
      phone: ''
    })
  }
  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <input
          placeholder="이름"
          value={this.state.name}
          onChange={this.handleChange}
          name="name"
        />
        <input
          placeholder="전화번호"
          value={this.state.phone}
          onChange={this.handleChange}
          name="phone"
        />
        <button type="submit">등록</button>
      </form>
    );
  }
}

export default PhoneForm;
```