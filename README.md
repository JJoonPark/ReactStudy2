# JSX
### JSX도 표현식이다.
컴파일이 끝나면, JSX 표현식이 정규 JavaScript 함수로 호출이 되고 JavaScript 객체로 인식된다.
즉, JSX를 if 구문 및 for loop 안에 사용하고, 변수에 할당하고, 인자로서 받아들이고, 함수로부터 반환할 수 있다.
```
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

### JSX 속성 정의
어트립뷰트에 다옴표를 이용해 문자열 리터럴을 정의할 수 있다.
```const element = <a href="https://www.reactjs.org"> link </a>;```
중괄호를 사용하여 어트리뷰트에 JavaScript 표현식을 삽입할 수도 있다.
```const element = <img src={user.avatarUrl}></img>;```
** JSX는 HTML보다는 JavaScript에 가깝기 때문에, React DOM은 HTML 어트리뷰트 이름 대신 camelCase 프로퍼티 명명 규칙을 사용한다. 예를 들어, JSX에서 class는 className가 되고 tabindex는 tabIndex가 된다. **

### JSX는 주입 공격을 방지한다.
JSX에 사용자 입력을 삽입하는 것은 안전하다.
```
const title = response.potentiallyMaliciousInput;
const element = <h1>{title}</h1>;
```
기본적으로 React DOM은 JSX에 삽입된 모든 값을 렌더링하기 전에 이스케이프 하므로, APP에서 명시적으로 작성되지 않은 내용은 주입되지 않는다. 모든 항목은 렌더링 되기 전에 문자열로 변환된다. 이런 특성으로 인해 XSS (cross-site-scripting)공격을 방지할 수 있다.

### JSX는 객체를 표현한다.
Babel은 JSX를 React.createElement() 호출로 컴파일한다.
다음의 두 예시는 동일하다.
```
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```
```
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```
React.createElement()는 버그가 없는 코드를 작성하는 데 도움이 되도록 몇 가지 검사를 수행하며, 기본적으로 다음과 같은 객체를 생성한다.
```
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```
이러한 객체를 "React 엘리먼트"라고 하며, 화면에서 보고 싶은 것을 나타내는 표현이라 생각하면 된다. React는 이 객체를 읽어서, DOM을 구성하고 최신 상태로 유지하는 데 사용한다.

### 생명주기 함수
ComponentDidMount()메서드는 컴포넌트 출력물이 DOM에 렌더링 된 후에 실행된다.(타이머를 설정하기에 좋은 장소)
```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```
1. &lt;Clock />가 ReactDOM.render()로 전달되었을 때 React는 Clock 컴포넌트의 constructor를 호출한다. Clock이 현재 시각을 표시해야 하기 때문에 현재 시각이 포함된 객체로 this.state를 초기화 한다.
2. React는 Clock 컴포넌트의 render() 메서드를 호출한다. 이를 통해 React는 화면에 표시되어야 할 내용을 알게 된다. 그 다음 React는 Clock의 렌더링 출력값을 일치시키기 위해 DOM을 업데이트 한다.
3. Clock 출력값이 DOM에 삽입되면, React는 componentDidMount() 생명주기 메서드를 호출한다. 그 안에서 Clock 컴포넌트는 매초 컴포넌트의 tick() 메서드를 호출하기 위한 타이머를 설정하도록 브라우저에 요청한다.
4. 매초 브라우저가 tick() 메서드를 호출한다. 그 안에서 Clock 컴포넌트는 setState()에 현재 시각을 포함하는 객체를 호출하면서 UI 업데이트를 진행한다. setState() 호출 덕분에 React는 state가 변경된 것을 인지하고 화면에 표시될 내용을 알아내기 위해 render() 메서드를 다시 호출한다. 이 때 render() 메서드 안의 this.state.date가 달라지고 렌더링 출력값은 업데이트된 시각을 포함한다. React는 이에따라 DOM을 업데이트 한다.
5. Clock 컴포넌트가 DOM으로부터 한 번이라도 삭제된 적이 있다면 React는 타이머를 멈추기 위해 componentWillUnmount() 생명주기 메서드를 호출한다.

# 엘리먼트 렌더링
엘리먼트는 React 앱의 가장 작은 단위이다. 브라우저 DOM 엘리먼트와 달리 React 엘리먼트는 일반 객체이며(plain object) 쉽게 생성할 수 있다. React DOM은 React 엘리먼트와 일치하도록 DOM을 업데이트한다.
** "컴포넌트"와 엘리먼트를 혼동할 수 있지만 엘리먼트는 컴포넌트의 "구성 요소"이다 **

### 변경된 부분만 업데이트하기
React DOM은 해당 엘리먼트와 그 자식 엘리먼트를 이전의 엘리먼트와 비교하고 DOM을 원하는 상태로 만드는데 필요한 경우에만 DOM을 업데이트 한다.

# Components와 Props
컴포넌트를 통해 UI를 재사용 가능한 개별적인 여러 조각으로 나누고, 각 조각을 개별적으로 살펴볼 수 있다.
개념적으로 Component는 JavaScript 함수와 유사하다. "props"라고 하는 임의의 입력을 받은 후, 화면에 어떻게 표시되는지를 기술하는 React 엘리먼트를 반환한다.

### 함수 Component와 Class Component
Component를 정의하는 가장 간단한 방법은 JavaScript 함수를 작성하는 것이다.
```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>
}
```
이 함수는 데이터를 가진 하나의 "props"(props는 속성을 나타내는 데이터이다) 객체 인자를 받은 후 React 엘리먼트를 반환하므로 유효한 React Component이다. 이러한 Component는 JavaScript 함수이기 때문에 말 그대로 "함수 Component"라고 호칭한다.

또한 ES6 class를 사용하여 컴포넌트를 정의할 수 있다.
```
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```
** React의 관점에서 볼 때 위 두 가지 유형의 컴포넌트는 동일하다. **
** 컴포넌트의 이름은 항상 대문자로 시작한다. **
React는 소문자로 시작하는 컴포넌트를 DOM 태그로 처리한다. 예를 들어 &lt;div />는 HTML div 태그를 나타내지만, &lt;Welcome />은 컴포넌트를 나타내며 범위 안에 Welcome이 있어야 한다.

### props는 읽기 전용이다.
함수 컴포넌트나 클래스 컴포넌트 모두 컴포넌트의 자체 props를 수정해서는 안된다.
```
function sum(a, b) {
  return a + b;
}
```
이런 함수들은 ** 순수 함수 ** 라고 호칭한다. 입력값을 바꾸려 하지 않고 항상 동일한 입력값에 대해 동일한 결과를 반환하기 때문이다.
반면에 다음 함수는 자신의 입력값을 변경하기 대문에 순수 함수가 아니다.
```
function withdraw(account, amount) {
  account.total -= amount;
}
```
** 모든 React 컴포넌트는 자신의 props를 다룰 때 반드시 순수 함수처럼 동작해야 한다. **

# State and Lifecycle
### State를 올바르게 사용하기
this.state를 지정할 수 있는 유일한 공간은 바로 constructor이다.

### State 업데이트는 비동기적일 수도 있다.
React는 성능을 위해 여러 setState() 호출을 단일 업데이트로 한꺼번에 처리할 수 있다.
this.props와 this.state가 비동기적으로 업데이트될 수 있기 때문에 다음 state를 계산할 때 해당 값에 의존해서는 안된다.
예를 들어, 다음 코드는 카운터 업데이트에 실패할 수 있다.
```
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```
이를 수정하기 위해 객체보다는 함수를 인자로 사용하는 다른 형태의 setState()를 사용한다. 그 함수는 이전 state를 첫 번째 인자로 받아들일 것이고, 업데이트가 적용된 시점의 props를 두 번째 인자로 받아들일 것이다.
```
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```
위에서는 화살표 함수를 사용했지만, 일반적인 함수에서도 정상적으로 작동한다.
```
// Correct
this.setState(function(state, props) {
  return {
    counter: state.counter + props.increment
  };
}));
```
### State 업데이트는 병합된다.
setState()를 호출할 때 React는 제공한 객체를 현재 state로 병합한다.
예를 들어, state는 다양한 독립적인 변수를 포함할 수 있다.
```
constructor(props) {
  super(props);
  this.state = {
    posts: [],
    comments: []
  };
}
```
별도의 setState() 호출로 이러한 변수를 독립적으로 업데이트할 수 있다.
```
componentDidMount() {
  fetchPosts().then(reponse => {
    this.setState({
      posts: reponse.posts
    });
  });
  fetchComments().then(reponse => {
    this.setState({
      comments: response.comments
    });
  });
}
```
병합은 얕게 이루어지기 때문에 this.setState({comments})는 this.state.posts에 영향을 주진 않지만 this.state.comments는 완전히 대체된다.

### 데이터는 아래로 흐른다.
부모 컴포넌트나 자식 컴포넌트 모두 특정 컴포넌트가 유상태인지 또는 무상태인지 알 수 없고, 그들이 함수느 클래스로 정의되었는지에 대해서 관심을 가질 필요가 없다.
이 때문에 state는 종종 로컬 또는 캡슐화라고 불린다. state가 소유하고 설정한 컴포넌트 이외에는 어떠한 컴포넌트에도 접근할 수 없다.
컴포넌트는 자신의 state를 자식 컴포넌트에 props로 전달할 수 있다.
```&lt;Formatteddate date={this.state.date} />```
FormattedDate 컴포넌트는 date를 자신의 props로 받을 것이고 이것이 Clock의 state로부터 왔는지, Clock의 props에서 왔는지, 수동으로 입력한 것인지 알지 못한다.

일반적으로 이를 "하향식(top-down)" 또는 "단방향식" 데이터 흐름이라고 한다. 모든 state는 항상 특정한 컴포넌트가 소유하고 있으며 그 state로부터 파생된 UI 또는 데이터는 오직 트리구조에서 자신의 "아래"에 있는 컴포넌트에만 영향을 미친다.

# 이벤트 처리하기
React 엘리먼트에서 이벤트를 처리하는 방식은 DOM 엘리먼트에서 이벤트를 처리하는 방식과 매우 유사하다. 몇 가지 문법 차이는 다음과 같다.
 1. React의 이벤트는 소문자 대신 캐멀 케이스(camelCase)를 사용한다.
 2. JSX를 사용하여 문자열이 아닌 함수로 이벤트 핸들러를 전달한다.
 3. React에서는 false를 반환해도 기본 동작을 방지할 수 없다. 반드시 preventDefault를 명시적으로 호출해야 한다. 예를 들어, 일반 HTML에서 폼을 제출할 때 가지고 있는 기본 동작을 방지하기 위해 다음과 같은 코드를 작성할 수 있다.
```
&lt;form onsubmit="console.log('You clicked submit.'); return false">
  &lt;button type="submit">Submit&lt;/button>
&lt;form>
```
React에서는 다음과 같이 작성할 수 있다.
```
function Form() {
  function handleSubmit(e) {
    e.preventDefault();
    console.log('You clicked submit.');
  }
  
  return (
    <form onSubmit={handleSubmit}>
      <button type="submit">Submit</button>
    </form>
  );
}
```
 

