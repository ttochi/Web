# React Basic Part_2

이번 코스에서는 React.js의 기본을 더 숙지하고 이를 결합한 기술인 `programming pattern`들을 배운다. 그리고 React.js를 로컬에 설치하는 방법을 배운다.

## 1. First Programming Pattern
지금까지 배운 React 기본 요소들을 결합하여 하나의 `programming pattern`을 배워보자.
단순한 패턴에서 시작해서 복잡한 패턴까지 확장시켜나갈 것이다.

### 1.1 Stateless Components Inherit From Stateful Components
`Stateful` 부모 컴포넌트에서 `Stateless` 자식 컴포넌트로 state를 전달하는 프로그래밍 패턴을 살펴보자.

> `Stateful component`는 state를 갖는 컴포넌트, `Stateless Component`는 state가 없는 컴포넌트

stateless 컴포넌트의 `props`로 `state`를 보내는 stateful 컴포넌트 클래스 `Parent`를 만들자.
```jsx
// Parent.js

var React = require('react');
var ReactDOM = require('react-dom');
var Child = require('./Child.js');

var Parent = React.createClass({
  getInitialState: function() {
    return { name: 'TTochi' }
  },

  render: function() {
    return <Child name={this.state.name} />;
  }
});

ReactDOM.render(<Parent />, document.getElementById('app'));
```

`Parent`로부터 props를 전달받는 컴포넌트 클래스 `Child`를 만들자.
```jsx
// Child.js

var React = require('react');

var Child = React.createClass({
  render: function() {
    return <h1>My name is {this.props.name}!</h1>;
  }
});

module.exports = Child;
```

위의 코드에서 `<Parent />`의 `state`가 `<Child />`의 `props`로 전달되었다.

앞서 Part_1에서 컴포넌트가 `this.setState` 함수를 통해 자신의 state를 바꿀 수 있다고 배웠다. 그렇다면 컴포넌트가 자신의 props도 바꿀 수 있을까?
```jsx
var Bad = React.createClass({
  render: function () {
    this.props.message = 'yo'; // NOOOOOOOOOOOOOO!!!
    return <h1>{this.props.message}</h1>;
  }
});
```

위의 코드와 같이 컴포넌트 내에서 props를 바꾸려하면 에러가 날 것이다.

props와 state는 dynamic information을 저장한다고 했다. 즉, 값이 동적으로 바뀌는 데이터를 저장한다는 건데 컴포넌트가 자신의 props를 바꿀 수 없다는 게 모순적으로 들릴 수 있다.

대신 props는 컴포넌트 밖에서 값을 변화시킬 수 있다. 이에 대해서 프로그래밍 패턴을 확장시켜보자!

### 1.2 Child Components Update Their Parents' state
이번에는 위의 패턴을 확장시켜서 자식 컴포넌트가 부모 컴포넌트의 state 값을 업데이트시키는 프로그래밍 패턴을 살펴보자.

1. [Define] 부모 컴포넌트에서 `this.setState`를 실행시키는 함수를 정의한다.
2. [Pass] 이 함수를 자식 컴포넌트의 `props`로 넘긴다.
3. [Receive] 자식 컴포넌트에서 이 함수를 받아서 이벤트 핸들러로서 활용한다.

위에서 작성한 코드에서 selector를 둬서 선택한 옵션에 따라 name 값을 변화시켜보자.

**Define and Pass Event Handler**

부모 컴포넌트에서 `this.setState`를 실행시키는 함수 `changeName`을 정의하고 이 함수를 자식 컴포넌트의 `props`로 넘긴다.
```jsx
// Parent.js

var React = require('react');
var ReactDOM = require('react-dom');
var Child = require('./Child.js');

var Parent = React.createClass({
  getInitialState: function() {
    return { name: 'TTochi' }
  },
  changeName: function(newName) {
    this.setState({
      name: newName
    });
  },
  render: function() {
    return (
      <Child 
          name={this.state.name}
          onChange={this.changeName} />
    );
  }
});

ReactDOM.render(<Parent />, document.getElementById('app'));
```

**Receive Event Handler**

자식 컴포넌트에서 props로 받은 `onChange` 함수를 이벤트 핸들러로서 활용시키자.
```jsx
// Child.js

var React = require('react');

var Child = React.createClass({
  render: function() {
    return (
      <div>
        <h1>My name is {this.props.name}!</h1>
        <select onChange={this.props.onChange} >
          <option value="TTochi">TTochi</option>
          <option value="Yujin">Yujin</option>
        </select>
      </div>
    );
  }
});

module.exports = Child;
```

이 때, `select`의 `onChange` 함수는 event object를 argument로 전달하기 때문에 `<Parent />`의 `changeName` 함수의 argument에서 받을 수 없다.

이는 React에서 이벤트 핸들러를 전달받을 때 흔히 생기는 문제이다.
이 때, 컴포넌트에 props로 전달받은 함수를 부르는 다른 새로운 함수를 정의하여 이 문제를 해결하면 된다.
```jsx
// Child.js

var React = require('react');

var Child = React.createClass({
  handleChange: function(e) {
    var name = e.target.value;
    this.props.onChange(name);
  },
  render: function() {
    return (
      <div>
        <h1>My name is {this.props.name}!</h1>
        <select onChange={this.handleChange} >
          <option value="TTochi">TTochi</option>
          <option value="Yujin">Yujin</option>
        </select>
      </div>
    );
  }
});

module.exports = Child;
```

새로운 함수 `handelChange`는 event object를 argument로 받고 여기서 value 값을 추출하여 props로 전달받은 `onChange` 함수의 argument로 전달한다.

1. 이제 select의 옵션을 변경하면 이벤트 핸들러로 `<Child />`의 `handleChange` 함수를 부른다.
2. 이 함수에서 props로 받은 `<Parent />`의 `changeName` 함수를 불러 `<Parent />`의 state 값을 바꾼다.
3. `<Parent />`의 `state` 값이 바뀌면서 `<Child />`로 전달하는 `props` 값이 바뀌며 새로 랜더링된다.
4. 이름이 바꼈다!

**Automatic Binding**

`changeName` 함수 내 `this.setState`에서의 `this`는 컴포넌트 클래스인 `Parent` 객체를 나타내야 한다. (그래야지 `<Parent />`의 state 값이 바뀔테니까)
하지만 사실상 `changeName` 함수를 `<Child />` 내에서 불렀기 때문에 `this`는 해당 함수가 불려진 `Child` 객체가 되야할 것이다.

하지만 React의 `automatic binding` 덕분에 props로 넘긴 함수에서의 `this`는 해당 함수가 정의된 곳에서의 객체를 가르킨다!

### 1.3 Child Components Update Sibling Components
위의 패턴을 계속해서 확장해나가자!

우리는 처음, `stateful` 부모 컴포넌트에서 `stateless` 자식 컴포넌트로 `state`를 `prop`으로 전달하는 패턴을 배웠다.

이 패턴을 확장시켜, `stateful` 부모 컴포넌트에서 `stateless` 자식 컴포넌트로 event handler를 넘기고 자식 컴포넌트에서 event handler를 활용해 부모 컴포넌트의 `state`를 업데이트하는 패턴을 배웠다.

이제 이 패턴을 또 확장시켜, 자식 컴포넌트에서 부모 컴포넌트의 state를 업데이트하고 부모 컴포넌트는 그 state를 sibling 컴포넌트로 넘기는 패턴을 배우자!

**Divide Child Component**

위에서 작성한 코드에서 `Child` 컴포넌트 클래스를 역할에 따라 나누자. 새로운 컴포넌트 클래스인 `Sibling`을 생성하자.
```jsx
// Child.js

var React = require('react');

var Child = React.createClass({
  handleChange: function(e) {
    var name = e.target.value;
    this.props.onChange(name);
  },
  render: function() {
    return (
      <select onChange={this.handleChange} >
        <option value="TTochi">TTochi</option>
        <option value="Yujin">Yujin</option>
      </select>
    );
  }
});

module.exports = Child;
```

```jsx
// Sibling.js

var React = require('react');

var Child = React.createClass({
  handleChange: function(e) {
    var name = e.target.value;
    this.props.onChange(name);
  },
  render: function() {
    return <h1>My name is {this.props.name}!</h1>;
  }
});

module.exports = Child;
```

`<Sibling />`는 정보를 display하는 역할을, `<Child />`는 정보의 값을 바꾸는 역할을 한다. 

**Rendering Two Child**

이제 `Parent` 컴포넌트 클래스에 두 자식 컴포넌트를 랜더링시키자.
```jsx
// Parent.js

var React = require('react');
var ReactDOM = require('react-dom');
var Child = require('./Child.js');
var Sibling = require('./Sibling');

var Parent = React.createClass({
  getInitialState: function() {
    return { name: 'TTochi' }
  },
  changeName: function(newName) {
    this.setState({
      name: newName
    });
  },
  render: function() {
    return (
      <div>
        <Child onChange={this.changeName} />
        <Sibling name={this.state.name} />
      </div>
    );
  }
});

ReactDOM.render(<Parent />, document.getElementById('app'));
```

이제 `<Child />`에서 이벤트가 발생하면 `<Parent />`에서 `this.setState`를 실행시켜 state 값을 업데이트 시키고 render 함수도 재실행된다. 그러면 업데이트된 state값이 `<Sibling />`에 반영이 되어 바뀐 props 값을 띄우게 되는 것이다.

즉, `<Child />`에서 `<Sibling />`의 특정 값을 바꾸고 싶다면 `<Parent />`의 state를 타고 값을 변경한다는 것!

> `Parent`의 render 함수에서 `<Sibling />`와 `<Child />`는 하나의 outer element로 싸서 반환해야 한다!

**Summary**

위의 패턴을 전반적으로 정리해보면,

* stateful 부모 컴포넌트에서 `this.setState`를 호출하는 함수를 정의한다.
* 이 함수를 stateless 자식 컴포넌트의 `props`로 넘긴다.
* stateless 자식 컴포넌트에서 `props`로 받아진 위의 함수를 호출하고 event object를 argument로 갖는 새로운 함수를 정의한다.
* stateless 자식 컴포넌트에서 이 함수를 event handler로서 활용하여 이벤트가 발생하였을 때 부모 컴포넌트의 `state`를 업데이트 시킨다.
* 부모 컴포넌트에서 업데이트시킬 `state` 값을 다른 자식 컴포넌트의 `props`로 넘긴다.

이렇게 부모 컴포넌트에서 자식 컴포넌트로 state를 props로 전달하고, 자식 컴포넌트는 부모 컴포넌트의 `setState` 함수를 불러서 sibling 컴포넌트의 props값을 변환하는 패턴에 대해 살펴봤다.

이 프로그래밍 패턴은 React에서 전반적으로 많이 쓰이는 패턴으로 익숙하게 사용하자.

## 2. Second Programming Pattern
새로운 프로그래밍 패턴에 대해서 배워보자. 하나의 거대한 컴포넌트에 대해서 작은 컴포넌트들로 쪼갤 수 있다.

하나의 컴포넌트를 `presentational` 컴포넌트와 `container` 컴포넌트로 분리하는 패턴에 대해 알아보면서 어떤 컴포넌트가 쪼개기 좋은지, 어떻게 쪼개는지에 대해 알아보자.

### 2.1 Container Components From Presentational Components
state를 가져야하는 컴포넌트에 대해서 (1)<u>state 값에 대한 로직을 관리하는 컴포넌트</u>와 계산된 state를 props로 받아 (2)<u>HTML-like JSX를 렌더링하는 컴포넌트</u>로 나누자.

> `presentational` 컴포넌트는 rendering을 위한 컴포넌트로,
> `container` 컴포넌트는 logic을 위한 컴포넌트로 사용한다.

**Code Example**

아래의 `GuineaPigs.js`를 보자.
```jsx
var React = require('react');
var ReactDOM = require('react-dom');

var GUINEAPATHS = ['url1', 'url2', 'url3', 'url4'];

var GuineaPigs = React.createClass({
  getInitialState: function () {
    return { currentGP: 0 };
  },

  nextGP: function () {
    var current = this.state.currentGP;
    var next = ++current % GUINEAPATHS.length;
    this.setState({ currentGP: next });
  },

  interval: null,

  componentDidMount: function () {
    this.interval = setInterval(this.nextGP, 5000);
  },

  componentWillUnmount: function () {
    clearInterval(this.interval);
  },

  render: function () {
    var src = GUINEAPATHS[this.state.currentGP];
    return (
      <div>
        <h1>Cute Guinea Pigs</h1>
        <img src={src} />
      </div>
    );
  }
});

ReactDOM.render(
  <GuineaPigs />, 
  document.getElementById('app')
);
```

위의 코드를 보면 `GuineaPigs`는 이미지를 랜더링시키고 일정 시간 간격으로 다음 이미지를 고르는 로지컬 작업을 진행한다. 이 `GuineaPigs`를 `presentational` 컴포넌트와 `container` 컴포넌트로 쪼개보자.

**Seperate Components**

아래와 같이 container 컴포넌트들을 다른 폴더로 분리하여 관리하겠다.
```
├── components
│   └── GuineaPigs.js
└── containers
    └── GuineaPigsContainer.js
```

일반적으로 컴포넌트마다 각각 하나의 파일에서 관리하고 파일명은 컴포넌트 클래스 이름을 따른다. 컴포넌트를 정의한 파일들은 components 폴더 하위에 관리된다.

한 컴포넌트가 다른 컴포넌트에 의해 랜더링 되는 경우에는 `module.export`와 `render`를 이용하여 file import를 한다.

**Presentational Component**

`GuineaPigs` 컴포넌트 클래스를 presentational 컴포넌트로 만들 것이다. 즉, HTML-like JSX를 렌더링하는 역할만 갖도록 해주자.

presentational 컴포넌트는 container 컴포넌트에 의해 랜더링되므로 `module.exports`를 시켜줘야한다.

다음은 presentational 컴포넌트를 정의한 GuineaPigs.js
```jsx
var React = require('react');

var GuineaPigs = React.createClass({
  render: function () {
    var src = this.props.src;
    return (
      <div>
        <h1>Cute Guinea Pigs</h1>
        <img src={src} />
      </div>
    );
  }
});

module.exports = GuineaPigs;
```

**Container Component**

`GuineaPigsContainer` 컴포넌트 클래스를 container 컴포넌트 클래스로 사용하자. HTML-lisk JSX를 랜더링하는 부분을 삭제하고 대신 `<GuineaPigs />`를 렌더링하자.

container 컴포넌트가 이미지 url을 선택하면 이를 presentational 컴포넌트로 넘겨야한다. 그러므로 `<GuineaPigs />`에 props로 url 정보를 넘겨주자.

```jsx
// GuineaPigsContainer.js
var React = require('react');
var ReactDOM = require('react-dom');
var GuineaPigs = require('../components/GuineaPigs')

var GUINEAPATHS = ['url1', 'url2', 'url3', 'url4'];

var GuineaPigsContainer = React.createClass({
  getInitialState: function () {
    return { currentGP: 0 };
  },

  nextGP: function () {
    var current = this.state.currentGP;
    var next = ++current % GUINEAPATHS.length;
    this.setState({ currentGP: next });
  },

  interval: null,

  componentDidMount: function () {
    this.interval = setInterval(this.nextGP, 5000);
  },

  componentWillUnmount: function () {
    clearInterval(this.interval);
  },

  render: function () {
    var src = GUINEAPATHS[this.state.currentGP];
    return <GuineaPigs src={src} />;
  }
});

ReactDOM.render(
  <GuineaPigsContainer />, 
  document.getElementById('app')
);
```

**Summary**

container 컴포넌트는 '무엇을' display 할 지, presentational 컴포넌트는 실제로 'display 하는' 역할을 한다. 각각의 부분이 많은 양의 코드로 짜여져 있다면 분리시켜야 할 타이밍!

**Why Container?**

* 컴포넌트에서 data-fetching 부분과 rendering 부분을 분리해서 고려할 수 있다.
* presentational 컴포넌트를 재활용시킬 수 있다.
* presentational 컴포넌트에 PropTypes를 설정할 수 있다.

> 이 [문서](https://medium.com/@learnreact/container-components-c0e67432e005)를 참고

## 3. Advanced React Techniques
이번에는 React 프로그래밍을 할 때 유용한 테크닉에 대해서 알아보자.

* How to use styles.
* How to make a stateless functional component
* How to make a propType
* How to write a form

### 3.1 Style
html tag의 style 속성으로 css 속성에 대한 object를 넘길 수 있다.
```jsx
var myStyle = <h1 style={{ color: 'red' }}>Hello world</h1>;
```

위의 코드는 style 속성 값으로 `{}`를 넣어 javascript를 읽고 그 안에 `{ color: 'red' }`라는 객체를 넣은 것이다.

여러 개의 css 속성을 사용하고 싶다면 따로 css 속성 객체를 정의하고 변수값으로 넣어주는 게 더 깔끔하게 보인다.
```jsx
var style = {
  color: 'darkcyan',
  background: 'mintcream'
};

var myStyle = <h1 style={style}>Hello world</h1>;
```

**Name Syntax**

javascript에서 css 속성의 name은 hyphenated-lowercase로 정의하는게 일반적이다.

하지만 React에서는 camelCase를 사용해야한다.
```jsx
// Error!
var styles = {
  'margin-top':       "20px",
  'background-color': "green"
};

// OK!
var styles = {
  marginTop:       "20px",
  backgroundColor: "green"
};
```

**Value Syntax**

javascript에서 css 속성의 value 값은 string으로 정의하는게 일반적이다. 그래서 value가 숫자값인 경우에 어떤 unit을 사용하는지 명시할 수 있다. ex) "450px", "20%"

React에서는 value 값을 숫자값으로 정의할 수 있다. 이 경우에 unit은 "px"를 쓴다고 알아서 가정된다.
unit을 명시해야 한다면 string으로 정의한다.
```jsx
// 30px
{ fontSize: 30 }
// 2em
{ fontSize: "2em" }
```

**Manage Common Style**

파일 전반에 거쳐 공통으로 재사용되는 style들은 하나의 파일에 모아 `module.exports`로 style 값을 추출하여 사용하면 관리하기 쉬워진다.

```jsx
// commonStyles.js

var blue  = 'rgb(139, 157, 195)';
var darkBlue  = 'rgb(059, 089, 152)';
var lightBlue = 'rgb(223, 227, 238)';

module.exports = {
  blue: blue,
  darkBlue: darkBlue,
  lightBlue: lightBlue
};
```

```jsx
var styles = require('./commonStyles');

var divStyle = {
  backgroundColor: styles.darkBlue,
  color:           styles.lightBlue
};

var Wow = React.createClass({
  render: function () {
    return <div style={divStyle}>Hello world</div>;
  }
});
```

### 3.2 Statelss Functional Components
위의 두번째 프로그래밍 패턴에서 presentational 컴포넌트는 결국 rendering 역할만 하는 컴포넌트이다.
그렇다면 presentational 컴포넌트는 다른 property 없이 오직 render 함수만을 갖는다는 것을 눈치챘을 것이다.

이렇게 render 메소드만 갖는 컴포넌트에 대해서는 `React.createClass`를 사용하는 대신 하나의 자바스크립트 함수로 정의할 수 있다!

이렇게 함수로 정의할 수 있는 컴포넌트에 대해 **stateless functional component**라고 부른다.
```jsx
// A component class written in the usual way:
var MyComponentClass = React.createClass({
  render: function(){
    return <h1>Hello world</h1>;
  }
});

// The same component class, written as a stateless functional component:
function MyComponentClass () {
  return <h1>Hello world</h1>;
}
```

**Pass Props to Stateless Functional Component**

stateless functional 컴포넌트에서 전달받은 props에 접근하기 위해서 함수에 파라미터를 하나 추가해주면 해당 파라미터는 자동으로 props object를 받는다.
```
function MyComponentClass (props) {
  return <h1>Hello {props.name}</h1>;
}
```

> 이 파라미터는 `props`라는 이름으로 주는게 일반적이다.

### 3.3 PropTypes
props에 대한 typechecking을 할 수 있는 `propTypes`에 대해 알아보자.

`propTypes`을 통해 컴포넌트에서 props로 예상했던 타입을 제대로 가져오는지 validation을 한다.
props가 누락되거나 원하는 타입으로 받아지지 않은 경우에 콘솔에 경고메세지를 띄워준다.

또한 `propTypes`를 통해 컴포넌트에 어떤 props를 넘겨야 하는지 쉽게 documentation을 할 수 있다.

이는 개발을 진행하면서의 유지 보수에 도움이 될 것이다.

**Apply PropTypes**

`propTypes`는 instruction object의 property로 정의되며 object를 value로 갖는다.
```jsx
var MessageDisplayer = React.createClass({
  propTypes: {
    message:   React.PropTypes.string.isRequired,
    style:     React.PropTypes.object.isRequired,
    isMetric:  React.PropTypes.bool.isRequired,
    miles:     React.PropTypes.number.isRequired,
    milesToKM: React.PropTypes.func.isRequired,
    races:     React.PropTypes.array.isRequired
  },
  render: function () {
    // todo...
  }
});
```

`propTypes` object의 property들은 컴포넌트가 받을 prop의 이름을 사용한다.
(이 property 하나하나가 `propType`)

value로는 `React.PropTypes.expected-data-type`을 써서 typechecking을 한다. 추가로 `.isRequired`를 붙이면 컴포넌트가 해당 prop를 받지 못했을 때 콘솔에 경고메세지를 띄워준다.

> `React.PropTypes`가 React v15.5부터 deprecated 되어서 대신 [prop-types 라이브러리](https://www.npmjs.com/package/prop-types)를 가져다 써야한다.

**Use in Stateless Functional Component**

그렇다면 instruction object를 사용하지 않는 stateless functional component에서는 어떻게 `propTypes`를 정의할 수 있을까?
```jsx
function Example (props) {
  return <h1>{props.message}</h1>;
}

Example.propTypes = {
  message: React.PropTypes.string.isRequired
};
```

위와 같이 함수 자신의 property로 `propTypes`를 정의해준다!

### 3.4 React Form
HTML의 form element는 내부적으로 자체 state를 갖는 점에서 React의 다른 DOM element들과는 다르다.

일반적으로 폼을 사용할 때 user가 데이터를 입력하고 submit 버튼을 눌러야 해당 데이터가 서버로 보내진다.

그렇게 되면 유저가 폼을 입력하는 도중에, 폼에서 유저가 입력한 데이터 값과 서버가 저장하고 있는 데이터 값이 다른 상태에서 유저가 입력한 데이터에 바로바로 접근해야하는 경우가 생긴다면 어떻게 해야할까?

input 태그에 대해서 value가 변할 때마다 데이터 값을 화면에 동기적으로 띄우는 코드를 작성해보자.
```jsx
var Input = React.createClass({
  getInitialState: function () {
    return {userInput: ''};
  },
  handleUserInput: function (e) {
    this.setState({userInput: e.target.value});
  },
  render: function () {
    return (
      <div>
        <input type="text"
          onChange={this.handleUserInput}
          value={this.state.userInput} />
        <h1>{this.state.userInput}</h1>
      </div>
    );
  }
});
```

input 태그에서 `onChange` 이벤트 리스너로 `handleUserInput`을 불러서 `setState`를 통해 현재 입력된 value로 state를 업데이트 시킨다. 
업데이트된 state 값은 다시 input element의 value값으로 반영이 되고 화면에 랜더링된다.
`<Input />`이 처음 랜더링될 때는 value값이 비어있어야 하므로 initialState로 빈 값을 설정한다.

**Controlled Component**

HTML에서, `<input>`, `<textarea>`, `<select>`와 같은 form element들은 보통 유저의 입력에 따라서 자신의 state를 저장하고 업데이트한다.
React에서, 값이 변하는 state값은 보통 컴포넌트의 state 속성으로 저장되고 `setState`에 의해 값이 업데이트 된다.

React의 state를 'single source of truth'로 받아들여 이 둘을 합칠 수 있다. 즉, form을 랜더링하는 React 컴포넌트가 form에서의 state 값도 control 하도록 하면 된다!

이런식으로 form의 state값을 관리하는 React 컴포넌트를 `controlled component`라고 부른다.

> [공식문서](https://facebook.github.io/react/docs/forms.html) 참조

## 4. Lifecycle Method
Lifecycle methods는 컴포넌트의 life에서 특정 시기에 불려지는 메소드들을 의미한다.

lifecycle methods로는 `mounting`, `updating`, `unmounting`가 있다.

### 4.1 Mounting
컴포넌트는 **처음** 랜더링될 때 'mount'되며, 이 때 컴포넌트의 `mounting` 메소드로 다음의 메소드들을 순차적으로 부른다.

* componentWillMount
* render
* componentDidMount

```jsx
var Example = React.createClass({
  componentWillMount: function () {
    alert('FOR THE FIRST TIME EVER...!');
  },
  componentDidMount: function () {
    alert('YOU JUST WITNESSED THE DEBUT');
  },
  render: function () {
    alert('RENDERING!');
    return <h1>Hello world</h1>;
  }
});

ReactDOM.render(<Example />, document.getElementById('app'));

setTimeout(function(){
  ReactDOM.render(<Example />, document.getElementById('app'));
}, 2000);
```

**componentWillMount**

`componentWillMount` 메소드는 컴포넌트가 render 되기 직전에 불려진다.

위의 코드를 실행시켰을 때 `componentWillMount` 함수가 먼저 실행되고 그 다음으로 `render` 함수가 실행된다.
2초 뒤, 컴포넌트가 다시 랜더링되어도 `componentWillMount` 함수는 실행되지 않는다.

**render**

render 함수는 'mounting lifecycle methods'와 'updating lifecycle methods'의 두 가지 lifecycle method에 속한다.

때문에 위의 코드에서 2초 뒤, 컴포넌트가 다시 랜더링 되었을 때 `render` 함수만이 실행되는 것을 확인할 수 있다.

**componentDidMount**

`componentDidMount` 메소드는 컴포넌트의 render가 로딩을 마친 직후에 불려진다.

위의 코드를 실행시켰을 때 `render` 함수가 실행되고 화면에 랜더링된 후 `componentDidMount` 함수가 실행된다.
2초 뒤, 컴포넌트가 다시 랜더링되어도 `componentDidMount` 함수는 실행되지 않는다.

> `componentDidMount` 메소드는 렌더링 된 직후라서 `ajax call`을 통해 API로부터 초기 데이터를 부를 때나, 렌더링 후 `setTimeout`이나 `setInterval`과 같이 타이머를 설정할 때 쓰기 좋을 곳이다.

### 4.2 Updating
컴포넌트는 두 번째 랜더링부터 랜더링될 때마다 항상 'update'가 된다. 이 때 컴포넌트의 `updating` 메소드로 다음의 메소드들을 순차적으로 부른다.

* componentWillReceiveProps
* shouldComponentUpdate
* componentWillUpdate
* render
* componentDidUpdate

**componentWillReceiveProps**

`componentWillReceiveProps` 메소드는 컴포넌트 rendering이 시작하기 전에 불려진다.
또, `componentWillReceiveProps` 메소드는 컴포넌트가 `props`를 전달받을 때에만 불려진다.

`componentWillReceiveProps` 메소드는 자동으로 컴포넌트가 업데이트되면서 받을 `props` 객체 `nextProps`를 argument로 받는다.

> `componentWillReceiveProps` 메소드는 새로 받아오는 props를 현재 컴포넌트의 state 값과 비교하여 무엇을 렌더링할지 결정할 때 주로 쓰인다.

**shouldComponentUpdate**

`shouldComponentUpdate` 메소드는 `componentWillReceiveProps` 메소드가 불린 후에, 컴포넌트 rendering이 시작되기 전에 불려진다.

`shouldComponentUpdate` 메소드는 boolean 값을 반환한다!
만약 `true`가 반환되면 업데이트를 계속 진행해나가지만, `false`가 반환되면 업데이트가 중단되어 이후의 메소드들은 불리지 않게 된다.

`shouldComponentUpdate` 메소드는 `nextProps`와 `nextState`를 argument로 받는다.

> `shouldComponentUpdate` 메소드는 특정 조건에 대해 false를 반환시켜 컴포넌트의 업데이트를 막을 때 사용된다.

**componentWillUpdate**

`componentWillUpdate` 메소드는 `shouldComponentUpdate` 메소드가 불린 후, 컴포넌트가 render 되기 직전에 불려진다.

`componentWillUpdate` 메소드는 `nextProps`와 `nextState`를 argument로 받는다.

**componentDidUpdate**

`componentDidUpdate` 메소드는 컴포넌트의 render가 로딩을 마친 직후에 불려진다.

`componentDidUpdate` 메소드는 `prevProps`와 `prevState`를 argument로 받는다.
`prevProps`와 `prevState`는 현재의 updating period가 시작하기 전의 props와 state를 나타내어 현재의 props와 state와 비교하기 위해 사용한다.

### 4.3 Unmounting
컴포넌트는 DOM으로부터 **제거될 때** 'unmount'되며, 컴포넌트의 `unmounting` 메소드로 다음의 메소드만을 실행한다.

* componentWillUnmount

이는 주로 DOM이 컴포넌트 없이 rerender될 때, 혹은 유저가 다른 페이지로 이동하거나 브라우저를 종료했을 때 발생한다.

**componentWillUnmount**

`componentWillUnmount` 메소드는 컴포넌트가 DOM으로부터 제거되기 직전에 불려진다.
주로 컴포넌트가 cleanup을 필요로하는 메소드를 실행했을 때 cleanup 작업을 하기 위해 사용한다.

-----------------

Codecademy의 튜토리얼을 마쳤다!

이제 실제로 로컬에서 설치하고 개발을 진행해보자!
[codecademy article](https://www.codecademy.com/articles/react-setup-i)의 5개 문서를 읽어보자. 
