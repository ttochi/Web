# React Basic Part_1

ReactJS는 페이스북 엔지니어들이 개발한 자바스크립트 라이브러리이다.

이번에 Codecademy를 통해 배운 React의 기초 내용을 정리한다.<br>
React의 가장 기본적인 개념인 JSX, components, props와 state에 대해 정리하였다.

## 1. JSX
```jsx
var h1 = <h1>Hello world</h1>;
```

위의 코드를 보면 javascript와 html 문법이 섞여있다.<br>
이렇게 javascript 코드 안에서 사용되는 html이 `JSX` 이다.<br>
JSX는 React에서 사용하기 위해 만들어진 syntax extension이다.<br>
JSX는 웹 브라우저가 읽을 수 있는 javascript 문법은 아니다.<br>
Javascirpt 파일에 JSX 코드가 섞여있다면 해당 파일은 `JSX compiler`를 통해 컴파일이 이루어져야 한다.

### 1.1 JSX element
```jsx
<h1>Hello world</h1>
```

JSX element는 위 코드와 같이 HTML와 매우 흡사하다!<br>
HTML처럼 nested도 가능하고 태그 속성도 자유롭게 넣을 수 있다.

JSX element는 자바스크립트처럼 사용이 가능하다. 즉, 변수값으로 지정될 수도 있고 함수에 전달될 수 있다. 또한 object나 array에 저장할 수도 있다.

> 만약 JSX element가 여러 줄을 차지하는 경우는 `()`를 통해 감싸줘야 한다.<br>
> 주의! 하나의 JSX element는 하나의 outermost tag를 갖는다.

JSX element를 선언하는 법을 배웠다. 이제 이 element를 어떻게 화면에 띄울 것인가.

### 1.2 Rendering JSX
`ReactDOM`은 리액트 메소드를 담고 있는 자바스크립트 라이브러리의 이름이다.<br>
`ReactDOM.render`를 통해서 JSX를 렌더링한다. JSX 표현에 대한 DOM node를 생성하고 트리에 이어붙인다.
```jsx
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(<h1>Hello world</h1>, document.getElementById('app'));
```

위의 코드에서처럼 `ReactDOM.render`의 첫번째 argument로 `<h1>Hello world</h1>`와 같이 렌더링 시키고자 하는 JSX element가 온다. 이 element는 이어지는 두번째 arguement에 append 된다.

### 1.3 Virtual DOM
`ReactDOM.render`는 변화가 생긴 DOM element에 대해서만 update를 한다.<br>
무슨말이냐 하면, 아래와 같은 코드를 실행시킨다고 할 때, 두번째 줄에 대한 코드는 수행되지 않는다는 것이다.
```jsx
var hello = <h1>Hello world</h1>;

// This will add "Hello world" to the screen:
ReactDOM.render(hello, document.getElementById('app'));

// This won't do anything at all:
ReactDOM.render(hello, document.getElementById('app'));
```

오직 필요한 DOM element만 업데이트 시킨다는게 React에서 정말 중요한 부분이다!<br>
이는 `virtual DOM`이라는 개념덕분에 가능한 것이다.

DOM manipulation은 다른 자바스크립트 기능보다 느리다. <u>대부분의 자바스크립트 프레임워크가 DOM을 필요 이상으로 자주 업데이트 하여 속도가 더 느려진다.</u>

> 가령, 10개 아이템을 담는 리스트의 첫번째 아이템을 지웠다고 하면 대부분의 자바스크립트 프레임워크는 이 리스트 자체를 rebuild 시킨다.

React에서는 모든 DOM 객체에 대해서 `virtual DOM`이 상응된다. `virtual DOM`은 DOM 객체를 나타낸다고 보면 되며 진짜 DOM 객체와 같은 속성을 갖는다. (하지만 진짜로 화면상에서 무언가를 바꾸지는 못한다.) DOM manipulation은 느리지만 `virtual DOM` manipulation은 화면에 그려지는게 없으므로 훨씬 빠르다.

JSX element를 렌더링할 때 모든 `virtual DOM`이 새로 업데이트된다. (비효율적이라고 느껴지지만 실제로 매우 빠른 속도로 업데이트 되므로 cost가 크진 않다)<br>
`virtual DOM`이 업데이트되면 React에서는 업데이트 된 `virtual DOM`과 업데이트 되기 직전 `virtual DOM`의 스냅샷을 비교하여 어떤 virtual DOM 객체가 변화하였는지를 잡아낸다. 이 과정을 `diffing`이라고 한다.

React에서 변화된 `virtual DOM` 객체에 대해서만 실제 DOM을 업데이트시킨다. 이렇게 필요한 DOM만 업데이트 시키므로써 퍼포먼스가 급격히 증가하는 것이다.

정리하자면 React에서 DOM을 업데이트 시키려고 할 때,

1. 모든 virtual DOM이 업데이트된다.
2. 업데이트 되기 전의 virtual DOM들과 비교하여 어떤 객체가 변화하였는지 잡아낸다.
3. 변화한 객체에 대해서만 real DOM에서 업데이트 시킨다.
4. 실제 DOM에서의 변화만이 화면 상에 나타나게 된다.

## 2. JSX Syntax

### 2.1 JSX vs HTML
JSX 문법은 HTML과 거의 유사하다. 하지만 몇 가지 주의할 차이점이 있다.

**class vs className**

HTML에서는,
```html
<h1 class="big">Hey</h1>
```

JSX에서는,
```jsx
<h1 className="big">Hey</h1>
```

JSX가 클래스를 클래스라고 부르지 못하는 이유는 `class`가 이미 javascript에서 사용되고 있기 때문이다. JSX가 랜더링 될 때는 `className`이 `class`로 자동으로 바뀐다.

**self-closing tag**

HTML에서는,
```html
<br />      // OK!
<br>        // OK!
```

JSX에서는,
```jsx
<br />      // OK!
<br>        //error!
```

HTML의 `<img>`나 `<input>`과 같이 closing tag가 없는 것들이 있다. 이런 태그가 `single-closing tag`이다. JSX에서는 반드시 이 태그들에 대해 `/`를 써주어야 한다.

### 2.2 Use Javascript in JSX
JSX expression 안에서 일반 javascript를 사용하려면 `{}`를 이용하자!
```jsx
<h1>{2 + 3}</h1>
```

이렇게 inject된 javascript 코드는 같은 파일에 있는 다른 javascript 코드와 동일한 환경에 있는 것이다. 즉, JSX element 외부에 선언된 변수나 함수에 접근이 가능하다는 것이다.

**Accessing Object**

JSX expression 안에서 JSX 외부에 선언된 변수에 접근이 가능하다.
```jsx
var name = 'TTochi';
var greeting = <p>Hello, {name}!</p>;
```

물론 html 태그 내부 뿐 아니라 태그의 attribute로도 활용한다.
```jsx
var pics = {
  panda: "http://bit.ly/1Tqltv5",
  owl: "http://bit.ly/1XGtkM3"
}; 

var panda = (
  <img 
    src={pics.panda} 
    alt="Lazy Panda" />
);
```

> JSX에서 코드 가독성을 위해 attribute가 많으면 한 줄씩 써준다고 함


또한 JSX element에서 Event Listener로 JSX 외부에 선언된 함수를 부를 수 있다.
```jsx
function myFunc () {
  alert('You Clicked!');
}

<img onClick={myFunc} />
```

> 주의! html에서는 `onclick`, `onmouseover`과 같이 event listener가 소문자로 구성되어 있지만 JSX에서는 `onClick`, `onMouseOver`로 쓴다

**Use Conditional**

JSX에서 사용되는 javascript에서는 `if문`과 `for문`을 사용할 수 없다!
```jsx
// ERROR!
var message = (
  <div>
    { if (isMorning) 'Good morning' else 'Good night' }
  </div>
);
```

그래서 if문을 JSX에 inject 시키지 않고 바깥에서 사용하거나
```jsx
if (isMorning) {
  var message = <div>Good morning</div>;
} else {
  var message = <div>Good night</div>;
}
```

간단한 조건문의 경우에는 `ternary operator`나 `&&` 를 이용하여 사용할 수 있다.
```jsx
var message = <div>{isMorning ? 'Good morning' : 'Good night'}</div>;
var message = <div>{isMorning && 'Good morning'}</div>;
```

### 2.3 Lists and Keys
JSX에서 array 형태로 저장된 list에 다음과 같이 접근할 수 있다.
```jsx
var liArray = [
  <li>item 1</li>, 
  <li>item 2<li>, 
  <li>item 3</li>
];

<ul>{liArray}</ul>

// This is same with below in JSX!
<ul>
  <li>item 1</li>
  <li>item 2</li>
  <li>item 3</li>
</ul>
```

이러한 특징과 `map`을 이용해서 간단한 list를 그려보자.
```jsx
var navigations = ['Home', 'Shop', 'About Me'];

var listItems = navigations.map(function(string){
  return <li>{string}</li>;
});

<ul>{listItems}</ul>
```

만약 이 코드를 그대로 돌리면 `key`값이 제공되어야 한다는 warning message가 뜬다.<br>
`key`는 list의 순서를 확실하게 정해주기 위해 JSX 내부적으로 사용되는 속성이다.
```jsx
var navigations = ['Home', 'Shop', 'About Me'];

var listItems = navigations.map(function(string, i){
  return <li key={'nav_' + i}>{string}</li>;
});

<ul>{listItems}</ul>
```

`key`는 react에서 어떤 아이템이 추가되고 변경되었는지 확인하기 위해 사용된다.

`key`를 이용하면 리스트에 새로운 아이템을 추가할 때 virtual DOM과 비교하여 실제 DOM에서 변경이 필요한 부분만 최소한으로 반영된다. 반면 `key`를 사용하지 않으면 이런 비교가 불가능하여 전체 리스트를 갱신하게 된다.

`key`는 각 item element에 stable한 identity를 주어야 하기 때문에 html의 `id` 속성과 같이 unique 값을 가져야 한다.

> [공식 문서 참고](https://facebook.github.io/react/docs/reconciliation.html#recursing-on-children)

### 2.4 createElement
사실 React에서 JSX expression이 없어도 코드를 짤 수 있다.
```jsx
var h1 = <h1>Hello world</h1>;
```

사실 React에서 JSX element를 컴파일할 때, 컴파일러가 이를 `React.createElement()` method로 변형시키는 것이다!
```jsx
var h1 = React.createElement(
  "h1",             // element
  null,             // props
  "Hello, world"    // ...children
);
```

JSX에 대해 기초적인 것을 알아보았다. 이제 본격적으로 React Components에 대해 알아보자!

## 3. React Components
`component`는 **reusable**한 작은 코드 chunck이다. `component`는 HTML을 rendering하는 일을 담당한다.

### 3.1 React & ReactDOM
**Import React**
```jsx
var React = require('react');
```

`require('react')`는 javascript object를 반환한다. 이 object는 react에서 사용되는 여러 method를 갖고 있다. 이 object가 `React library`이다.

JSX element가 컴파일 될 때 부르는 `React.createElement`도 React library method 이다. 때문에, JSX를 사용하기 전에 반드시 `React`라는 변수명으로 `require('react')`를 선언해야 한다.

**Import ReactDOM**
```jsx
var ReactDOM = require('react-dom');
```

`require('react-dom')`도 `require('react')`처럼 javascript object를 반환한다. 이 object 또한 react와 관련된 method를 갖고 있다.

`require('react-dom')`은 `ReactDOM.render`와 같이 DOM과 상호작용하는 method를 갖는다. 반면 `require('react')`는 오직 React 안에서만 쓰이는 method를 갖는다.

### 3.2 Component Class
모든 `component`는 `component class`에서 온다. component class는 component를 만드는 공장과도 같은 느낌인데, component class에서 component를 원하는 만큼 만들어낼 수 있다.

Component class를 만들려면 React library method인 `React.createClass`를 사용한다.<br>
Component class를 계속 재사용하기 위해 변수에 저장해놓는다. 이 때, **변수명은 반드시 대문자로 시작해야한다!**
```jsx
var MyComponentClass = React.createClass({
  render: function () {
    return <h1>Hello world</h1>;
  }
});
```

`React.createClass`는 하나의 argument를 갖는다. 그 argument는 `instruction object`로 component class에 어떤 component를 생성하는지 알려준다.

**instruction object**
```jsx
var instructionObject = {
  render: function () {
    return <h1>Hello world</h1>;
  }
};
```

`instruction object`는 `render` 함수를 포함한다. render 함수는 JSX expression을 return한다.<br>
왜 render 함수를 포함한 object가 instruction object가 되는지는 뒤에서 설명한다.

> 위의 코드는 설명을 위해 instruction object를 변수에 따로 저장하였지만 보통은 `React.createClass` 안에서 바로 선언한다

**Create Component Instance**

자, 이제 component class인 `MyComponentClass`를 이용해 component를 생성해보자. 매우 간단하다!
```jsx
<MyComponentClass />
```

위와 같이 component class의 이름을 갖는 JSX element를 생성한다. 이렇듯 JSX element는 HTML 태그도, component instance도 될 수 있는데, component class가 무조건 대문자로 시작하기 때문에 HTML 태그와 구분지을 수 있는거다!

**Render Component**

자, `React.createClass`는 `instruction object`를 argument로 갖는다.<br>
component class가 component를 생성할 때, 해당 component는 그 component class가 갖는 instruction object를 상속하게 된다. 즉, `<MyComponentClass />` component는 `MyComponentClass`의 render 함수를 갖는 것이다.

component가 render 함수를 갖고있기 때문에 DOM에 렌더링 시킬 수 있게 된다.<br>
component의 render 함수를 부르기 위해 해당 component를 `ReactDOM.render`에 전달한다.
```jsx
ReactDOM.render(
  <MyComponentClass />,
  document.getElementById('app')
);
```

`ReactDOM.render`가 `<MyComponentClass />` component의 render 함수를 실행시켜 `<h1>Hello world</h1>`를 return 시키고 이를 첫번째 argument로 가져 virtualDOM에 추가시키면서 화면에 렌더링시킨다.

이로써 component를 rendering 하는 것까지 알아봤다!

### 3.3 JSX in React Component
이제 JSX와 React component를 함께 사용해보자.
```jsx
var React = require('react');
var ReactDOM = require('react-dom');

var MyName = React.createClass({
  name: 'TTochi',
  clickFunc: function () {
    alert('You Clicked!');
  },
  render: function () {
    var target;
    if (Math.random() < 0.5) {
      target = 'My';
    } else {
      target = 'Your';
    }
    return (
      <h1 onClick={this.clickFunc}>
        {target} name is {this.name}.
      </h1>
    );
  }
});

ReactDOM.render(<MyName />, document.getElementById('app'));
```

당연한 이야기지만,

* component class에서 multi-line, `{}` 등 위에서 배운 JSX 문법을 그대로 사용할 수 있다. 
* render 함수에 다양한 로직을 넣을 수 있다.
* `this`로 createClass object에 다른 속성값을 정의하고 invoke할 수 있다.

## 4. Components Interacting
React에는 수많은 component들이 쓰인다. 이 component들이 결합되었을 때 그 효과는 매우 크다.<br>
React의 장점은 component 그 자체에서 오는 게 아니라, component interaction에서 온다.

### 4.1 Components Render Other Components
Component class를 생성해보자.
```jsx
var Example = React.createClass({
  render: function () {
    return <h1>Hello world</h1>;
  }
});
```

지금까지 component class의 render 함수에서 HTML 태그 형태의 JSX를 반환하는 것을 보았다.<br>
하지만 render 함수에서 component instance도 반환할 수 있다는 것!
```jsx
var OMG = React.createClass({
  render: function () {
    return <h1>Whooaa!</h1>;
  }
});

var Crazy = React.createClass({
  render: function () {
    return <OMG />;
  }
});
```

또한 3.2에서 `ReactDOM.render`에 의해 component의 render 함수가 실행되는 것을 배웠다. 하지만 여기서는 `Crazy component`의 render 함수에 의해 `OMG component`의 render 함수가 실행되는 것!

### 4.2 Import File
실제로 다른 component를 render하는 component를 만들어보자.

**require(filepath)**

`ProfilePage.js` 파일에서 `ProfilePage` 컴포넌트 클래스를 정의하고 있으며 `NavBar.js` 파일에서 정의된 `NavBar` 컴포넌트를 render 해야하는 상황을 가정해보자.

하지만 Javascript에서 다른 파일에서 정의된 객체를 불러올 수 없다!<br>
때문에 `ProfilePage.js`에서 `NavBar.js` 파일을 import 시켜야하는데, 이때 `require` method에 파일경로를 값으로 넣으면 import가 가능하다!
```jsx
var NavBar = require('./NavBar.js');
```

> 이런 방식의 모듈 시스템은 React에서만 쓰이는 방법은 아니다!<br>
> React의 모듈 시스템은 `Node.js`의 방식을 가져온 것!

**module.export**

`ProfilePage.js` 파일에 `NavBar.js` 파일을 불러오는 데 성공했다.<br>
하지만 이 파일이 매우 거대하다면? 필요 없는 로드가 일어날 것이다.

우리가 필요로 하는 부분은 `NavBar` 컴포넌트 클래스이다. 어떻게 `NavBar` 컴포넌트 클래스만 가져올 것인가?

답은 `module.exports`를 사용하는 것이다.
```jsx
// in NavBar.js file

var NavBar = React.createClass({
  render: function () {
    var pages = ['home', 'shop', 'about', 'contact'];
    var navLinks = pages.map(function(page){
      return <a href={'/' + page}>{page}</a>;
    });
    return <nav>{navLinks}</nav>;
  }
});

module.exports = NavBar;
```

`NavBar.js` 파일에서 다른 파일에서 사용할 expression을 `module.exports`에 넣어준다.<br>
그러면 `ProfilePage.js`에서 부른 `require('./NavBar.js')`는 파일 전체가 아닌 `module.export`에 부여된 `NavBar`를 가져오게 된다.
```jsx
// in ProfilePage.js file

var NavBar = require('./NavBar.js');

var ProfilePage = React.createClass({
  render: function () {
    return (
      <div>
        <NavBar />
        <h1>My Profile Page</h1>
        <p>I am TTochi</p>
      </div>
    );
  }
});

ReactDOM.render(<ProfilePage />, document.getElementById('app'));
```

`require('./NavBar.js')`로 가져온 `NavBar` 컴포넌트 클래스를 `ProfilePage.js`에서의 새로운 변수 `NavBar`로 정의하여 `ProfilePage` 컴포넌트 클래스의 render함수에서 component instance로 부른다.

이렇게 component에서 다른 component를 렌더링하면 단순한 component들로도 충분히 복잡한 구조를 만들어낼 수 있다.

## 5. Props
지금까지 component가 다른 component를 rendering하여 interaction하는 것을 살펴보았다.<br>
이번에는 서로 다른 component 간의 interaction 방식을 알아보겠다.

한 component가 다른 component로 정보를 전달할 수 있다!<br>
한 component에서 다른 component로 전달되는 정보가 바로 `props`이다!

### 5.1 Passing and Accessing Props
모든 component는 `props`라는 컴포넌트에 대한 정보를 담고있는 객체를 갖는다.<br>
`this.props`로 컴포넌트의 `props` 객체에 접근할 수 있다.

**Passing Props**

컴포넌트에 attribute 값을 줌으로써 우리는 컴포넌트에 props를 전달할 수 있다!<br>
attribute의 이름은 마음대로 지정할 수 있다.
```jsx
<MyComponent foo="bar" />
```

전달 값이 string이 아니라면 반드시 `{}`로 감싸주어야 한다.
```jsx
<MyInfo name="TTochi" age={2} male={false} favorite = {["aaa", "bbb", "ccc"]} />
```

자 이제, `MyInfo` 컴포넌트에서 `this.props`로 props 정보를 확인하면 다음과 같은 정보가 담겨있을 것이다.
```json
{
  "name": "TTochi",
  "age": 2,
  "male": false,
  "favorite": ["aaa", "bbb", "ccc"]
}
```

**Accessing Props**

props를 전달받은 컴포넌트에서 props에 접근하는 방법은 매우 간단하다.<br>
컴포넌트 내에서 컴포넌트의 props라는 객체의 특정 정보에 접근하려면 당연히 `this.props.name`을 이용하면 된다!

> 컴포넌트에서 전달받은 정보를 담고있는 객체를 `props`<br>
> 그 객체의 요소(정보) 하나 하나가 `prop`

### 5.2 Using Props in Component
보통은 `props`를 다음과 같이 한 컴포넌트에서 다른 컴포넌트로 정보를 전달할 때 주로 사용한다.
```jsx
var App = React.createClass({
  render: function () {
    return (
      <div>
        <Greeting name="TTochi" />
      </div>
    );
  }
});

var Greeting = React.createClass({
  render: function () {
    return <h1>Hi there, {this.props.name}!</h1>;
  }
});
```

> 보통 이때 `App` component를 `parent component`, `Greeting` component를 `child component`라 한다.

**Props as Event Handler**

컴포넌트 내에서 `props`는 태그 속성으로도, 변수값으로도, 함수로도 다양하게 사용될 수 있다.<br>
그 중에서 `props`를 event handler 함수로 사용하는 방법에 대해 알아보자.

우선, props로 넘길 event handler 함수를 정의하자.<br>
event handler 함수는 **component class의 instruction object 안에서 정의**되는 것이 일반적이다.
```jsx
var Parent = React.createClass({
  handleClick: function () {
    alert('You Clicked!');
  },
  render: function () {
    return <Child />;
  }
});
```

이제 `Parent` 컴포넌트에서 이 함수를 `Child` 컴포넌트의 props로 넘기자!
```jsx
var Parent = React.createClass({
  myFunc: function () {
    alert('You Clicked!');
  },
  render: function () {
    return <Child onClick={this.handleClick} />;
  }
});
```

`Child` 컴포넌트에서 props로 받은 함수를 이벤트 핸들러로 불러보자.
```jsx
var Child = React.createClass({
  render: function () {
    return (
      <button onClick={this.props.onClick}>
        Click me!
      </button>
    );
  }
});
```

이 때 함수 이름과 props 이름에 대한 **naming convention**이 있다!

* `Parent` 컴포넌트에서 정의된 event handler 함수의 이름은 이벤트 타입에 따라 `handleClick`, `handleKeyPress` 같이 쓰인다.
* `Child` 컴포넌트로 넘기는 `prop` 이름은 event 속성 이름을 그대로 따라 `onClick`, `onKeyPress` 같이 쓰인다.

### 5.3 this.props.children

모든 `props` 객체는 `children`이라는 이름의 속성을 갖는다.

지금까지는 우리가 컴포넌트를 생성할 때 `<MyComponentClass />`와 같이 self-closing tag를 이용했지만 사실 opening tag와 closing tag를 이용해서도 컴포넌트를 생성할 수 있다.
```jsx
<MyComponentClass />  // it works!

<MyComponentClass>    // it also works!
</MyComponentClass>
```

`this.props.children`은 컴포넌트의 JSX opening tag와 closing tag 사이에 있는 모든 값을 반환한다!
```jsx
// this.props.children returns "Hello world!"
<BigButton>Hello world!</BigButton>

// this.props.children returns array of <li>
<BigButton>
  <li>1</li>
  <li>2</li>
  <li>3</li>
</BigButton>

// this.props.children returns LilButton component
<BigButton>
  <LilButton />
</BigButton>

// this.props.children returns undefined
<BigButton />
```

### 5.4 getDefaultProps

컴포넌트에서 `prop`을 전달받지 못했을 때, `getDefaultProps` 함수를 이용하면 default 값을 정의할 수 있다.
```jsx
var Button = React.createClass({
  getDefaultProps: function() {
    return {text: 'I am a button'};
  },
  render: function () {
    return <button>{this.props.text}</button>;
  }
});
```

`getDefaultProps` 함수는 object를 반환하며, 반환할 object 내에서 default 속성을 미리 정의해두면 된다.

## 6. State
React components는 값이 유동적으로 변할 수 있는 `Dynamic information`을 필요로 한다. 이렇게 고정되지 않은 information을 얻기 위해 `props`와 `state`를 사용하며 그 외의 컴포넌트 속성들은 항상 고정된 값을 갖는다.

지금까지 `props`에 대해서 살펴보았고 이제 `state`에 대해 알아보자.

### 6.1 Initial State
밖으로부터 값을 받아오는 `props`와는 달리, `state`는 컴포넌트 내에서 값을 정한다.<br>
컴포넌트가 `state`를 갖도록 `getInitialState` 함수를 정의해보자!
```jsx
var App = React.createClass({
  getInitialState: function() {
    return {title: 'Best App'};
  },
  render: function () {
    return <h1>{this.state.title}</h1>;
  }
});
```

`App` 컴포넌트는 `{title: 'Best App'}`이라는 내용의 `state` 객체를 갖는다.<br>
컴포넌트의 state 객체의 특정 정보에 접근하려면 당연히 `this.props.name`을 이용하면 된다!

### 6.2 Change State
컴포넌트는 `state`값을 읽어올 뿐만 아니라, `setState` 함수를 통해 값을 변경시킬 수도 있다.<br>
`setState` 함수는 업데이트 시킬 `state` 객체와 콜백함수를 argument로 갖는다. (콜백함수는 사용되진 않는다.)

다음과 같은 state 객체를 갖는 컴포넌트가 있다고 해보자.
```json
{
  "mood": "great",
  "hungry": false
}
```

이 컴포넌트 내에서 아래와 같이 `this.setState`를 불러보자.
```jsx
this.setState({ hungry: true });
```

위의 함수가 불려진 후의 state 객체는 다음과 같다!
```json
{
  "mood": "great",
  "hungry": true
}
```

`this.setState`에서 받은 객체는 현재의 state 객체에 merge 되어지는 것이라서 `mood` 속성은 아무 영향을 받지 않는 것이다.<br>
만약 현재의 state 객체에는 없는 속성이 `this.setState`에서 받은 객체에 포함되면 state 객체에 새로운 속성이 하나 추가되는 것이다.

### 6.3 Using State in Component
이제 실제 컴포넌트 내에서 `state`에 접근해보고 `this.setState`를 통해 그 값을 바꾸는 작업을 진행해보자.

일반적으로 컴포넌트 내에서 `this.setState`를 부를 때, 그 자체를 부르기 보다는 이를 포함하는 함수를 따로 정의하여 부른다.
```jsx
var green = '#39D1B4';
var yellow = '#FFD712';

var Toggle = React.createClass({
  getInitialState: function() {
    return {
      color: green
    };
  },

  changeColor: function() {
    var newColor = (this.state.color == green) ? yellow : green;
    this.setState({ color: newColor });
  },

  render: function () {
    return (
      <div style={{background: this.state.color}}>
        <button onClick={this.changeColor}>
          Change color
        </button>
      </div>
    );
  }
});

ReactDOM.render(<Toggle />, document.getElementById('app'));
```

위는 버튼을 클릭할 때마다 배경색이 바뀌는 react 코드의 일부이다.

1. `Toggle` 컴포넌트의 `state` 객체가 `getInitialState` 함수로 정의되어있다.
2. button의 `onClick` 이벤트 핸들러로 컴포넌트의 `changeColor` 함수를 부른다.
3. `changeColor` 함수가 불릴 때마다 `this.setState`를 통해 `state` 객체의 `color` 속성을 바꾼다.

자, 이 때 `changeColor` 함수는 `state` 객체의 속성만 바꿀 뿐, 이를 화면에 렌더링하지 않는다!<br>
(`Toggle` 컴포넌트에서 render 함수를 부르는 곳은 `ReactDom.render` 밖에 없다)

그런데 어떻게 버튼을 클릭할 때마다 배경색이 바로 바뀌어서 화면에 렌더링까지 되는걸까?

왜냐하면, `this.setState`가 불릴 때마다 state 값을 바꾼 즉시, **자동으로** `render` 함수까지 부르기 때문이다!

그렇기 때문에 **절대로** `render` 함수 내에서 `this.setState`를 부르면 안된다!<br>
(render 함수 내에서 `this.setState`를 부르면 결국 무한루프에 빠지게 된다)

-----

React 앱은 기본적으로 component들을 정의하고 component에서 state를 설정하고 props를 넘기면서 진행된다. 이제 이런 일련의 과정들을 이해하였다.

Part2에서는 programming pattern을 배우고 지금까지의 내용을 종합하여 더 큰 규모의 기술을 다뤄보자!
