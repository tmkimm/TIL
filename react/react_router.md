# Router 기초 - Route, Link, 서브 Route

아래 내용은 velopert님 강의를 정리한 내용입니다.
 출처 : https://velopert.com/3275

---

## React Router는 URL 라우팅을 지원해준다.

Route는 최소 두가지 속성을 가진다.

- path 속성은 경로에 적용되는 URL 패턴
- component 속성을 필요한 컴포넌트를 렌더링하기 위한 속성

먼저, 프로젝트를 생성하고 패키지를 설치해주자

```bash
create-react-app myRouter  // 프로젝트 생성
npm install react-router-dom  // react-router 같이 설치됨
```

### 기본 사용법

react-router-dom을 import 후<Router>, <Route>를 이용해 경로를 잡아주면 된다.

<Route> 컴포넌트는 해당 Path로 들어왔을때 처리할 component를 지정해준다.

[http://localhost:3000/](http://localhost:3000/)나 [http://localhost:3000](http://localhost:3000/)/about로 접근해보면 해당 컴포넌트가 보인다.

하지만 사용자가 수동으로 URL를 입력해서 들어오진 않는다.

<Link> 컴포넌트를 이용해서 페이지 이동을 처리해보자.

```jsx
import React from 'react';
import { BrowserRouter as Router, Route } from 'react-router-dom';

import Home from './routes/Home';
import About from './routes/About';

const App = () => {
  return (
    <Router>
    <div>
      <Route exact path="/" component={Home}/>   // URL 중복을 막아줌
      <Route path="/about" component={About}/>
    </div>
    </Router>
  );
};

export default App;
```

### Link 컴포넌트

우선, 상단 네비게이션 역할을 해주는 Header 컴포넌트를 추가해보자

App

```jsx
import React from 'react';
import { BrowserRouter as Router, Route } from 'react-router-dom';

import Home from './routes/Home';
import About from './routes/About';

import Header from './components/Header';

const App = () => {
  return (
    <Router>
      <div>
        <Header/>
        <Route exact path="/" component={Home}/>
        <Route path="/about/:username" component={About}/>
      </div>
    </Router>
  );
};

export default App;
```

<Link> 컴포넌트는 <a> 태그와 동일하다. 개발자 모드에서 확인할 수 있다.

URL 파라미터를 받고싶을 경우 Route의 path를 "/about/:username" 이런 식으로 잡아주면 된다.

Link 컴포넌트에서는 <Link to="/about/tmkimm" className="item">소개</Link>

Header

```jsx
import React from 'react';
import { Link } from 'react-router-dom';
import './Header.css';

const Header = () => {
    return (
        <div className="header">
            <Link to="/" className="item">홈</Link>
            <Link to="/about/tmkimm" className="item">소개</Link>
        </div>
    );
};

export default Header;
```

실제 보여지는 About 컴포넌트의(Route에서 지정)  코드는 아래와 같다.

{match.params.username}으로 사용하면 된다.

함수형 컴포넌트가 아닐 경우 {this.props.match.params.username}이 되겠다.

```jsx
import React from 'react';

const About = ({match}) => {
    return (
        <div>
           {match.params.username}의소개 
        </div>
    );
};

export default About;
```

### 서브 라우트

예를 들어 포스트 카테고리에서 여러 포스트 리스트가 보여야 한다고 가정해보자.

1. 먼저 네비게이션에 포스트 카테고리를(컴포넌트를) 추가해야겠고
2. 포스트 컴포넌트 안에서는 서브 라우팅을 해야겠다.

포스트 카테고리를 추가하려면 App에 메인 라우트를 추가하고, Header에 링크를 추가한다.

App(라우트 추가)

```jsx
import Posts from './routes/Posts';
...
<Route path="/posts" component={Posts}/>
```

Header(링크 추가)

```jsx
<Link to="/posts" className="item">포스트</Link>
```

서브 라우팅(Posts, Post 컴포넌트)

```jsx
import React from 'react';
import { Route, Link } from 'react-router-dom';   // Route, Link imort

const Post = ({match}) => {  // Post 컴포넌트(글 상세)
    return (
        <h2>{match.params.title}</h2>  // URL 파라미터 값 표시
    );
}

const Posts = () => {
    return (
        <div>
            <Link to="/posts/pizza">피자</Link>  // Link. Header 컴포넌트에서 사용과 동일
            <Link to="/posts/chicken">치킨</Link>
            <Route path="/posts/:title" component={Post}/>  // 서브 라우트
        </div>
    );
};

export default Posts;
```

### NavLink 컴포넌트(엑티브 스타일)

그냥 Link 컴포넌트를 사용하여도 무방하지만 NavLink 컴포넌트를 이용하면 활성화(active) 되었을때 스타일을 더 편하게 줄수 있다.

우선, active 되었을때 지정할 css class를 추가해준다.

```css
.item:active, .item.active {
    background: white;
    color: #5c5cfa;
}
```

Link 컴포넌트를 사용하던 부분들 NavLink 컴포넌트로 변경해주고, activeClassName을 지정해준다.

상위 경로에(중복 되는 경로에) exact를 붙여준 이유는 중첩되는 부분들 제거하기 위함이다.

```jsx
import React from 'react';
import { NavLink } from 'react-router-dom';
import './Header.css';

const Header = () => {
    return (
        <div className="header">
           <NavLink exact to="/" className="item" activeClassName="active">홈</NavLink>
           <NavLink to="/about/tmkimm" className="item" activeClassName="active">소개</NavLink>
           <NavLink to="/posts" className="item" activeClassName="active">포스트</NavLink>
        </div>
    );
};

export default Header;
```