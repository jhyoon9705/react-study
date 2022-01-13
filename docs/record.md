# 2022.01.13

---

- [Props](#props)
  - [1. defaultProps](#1-defaultprops)
  - [2. propTypes](#2-proptypes)
- [State](#state)
  - [1. 클래스형 컴포넌트의 state](#1-클래스형-컴포넌트의-state)
  - [2. 함수형 컴포넌트의 state](#2-함수형-컴포넌트의-state)
  - [3. state를 사용할 때 주의사항](#3-state를-사용할-때-주의사항)
- [이벤트 핸들링](#이벤트-핸들링)
  - [1. input 여러 개 다루기](#1-input-여러-개-다루기)
  - [2. onKeyPress 이벤트 핸들링](#2-onkeypress-이벤트-핸들링)
- [ref: DOM에 이름달기](#ref-dom에-이름달기)
  - [1. DOM을 꼭 사용해야 하는 상황](#1-dom을-꼭-사용해야-하는-상황)
  - [2. Callback 함수를 이용한 ref 설정(class type components)](#2-callback-함수를-이용한-ref-설정class-type-components)
  - [3. createRef를 이용한 ref 설정(class type components)](#3-createref를-이용한-ref-설정class-type-components)
  - [4. Component에 ref 달기](#4-component에-ref-달기)

---

## Props

: 컴포넌트가 사용되는 과정에서 부모 컴포넌트가 설정하는 값

### 1. defaultProps

- props 값을 따로 지정하지 않았을 때 보여줄 기본값을 설정

```js
MyComponent.defaultProps = {
  name: 'newName',
};
```

### 2. propTypes

- 컴포넌트의 필수 props를 지정하거나 props의 type을 지정
- 코드 상단에 `import PropTypes from 'prop-types';` 필요
- 클래스 내부에서 사용하는 경우 static으로 선언하여 사용

```js
import PropTypes from 'prop-types';

...

MyComponent.propTypes = {
  name: PropTypes.string // name 값은 무조건 string으로 전달
  id: PropTypes.number.isRequired // id 값은 number를 필수 props로 지정
}
```

<br/>

## State

: 컴포넌트 내부에서 바뀔 수 있는 값

### 1. 클래스형 컴포넌트의 state

- state 설정: constructor 메소드를 작성하여 설정
- state 조회: `this.state` 사용
- state 변경: `this.setState()` 사용
- setState가 끝난 후 특정 작업 실행: `setState()`의 두 번째 인자로 Callback 함수 등록

```js
constructor(props) {
  super(props); // 반드시 호출
  // state의 초기값 설정
  this.state = {
    number: 0
  };
  ...
  render() {
    const {number} = this.state;
    return (
      ...
      this.setState({number: number + 1});
      ...
    )
  }
}
```

### 2. 함수형 컴포넌트의 state

- `useState()` 사용

```js
const [message, setMessage] = useState('');
// message는 현재 상태, setMessage는 상태를 바꿔주는 함수
const onClickEnter = () => setMessage('Hello!');
```

### 3. state를 사용할 때 주의사항

- state의 값을 바꾸어야 할 때에는 `setState` 또는 `useState`를 통해 전달받은 setter 함수를 사용
- **배열이나 객체를 업데이트 할 때에는, 해당 데이터의 사본을 만들고 그 사본에 값을 덥데이트한 후, 그 사본의 상태를 setState 혹은 setter 함수를 통해 업데이트**

```js
// 객체
const object = { a: 1, b: 2, c: 3 };
const newObject = { ...object, b: 2 }; // 사본을 만들어서 b 값만 덮어쓰기

// 배열
const array = [
  { id: 1, value: true },
  { id: 2, value: true },
  { id: 3, value: false },
];
let Array = array.concat({ id: 4 });
newArray = filter((item) => item.id !== 2);
newArray.map((item) => (item.id === 1 ? { ...item, value: false } : item));
```

<br/>

## 이벤트 핸들링

### 1. input 여러 개 다루기

- "event 객체를 활용" 또는 "`onChange` 관련 함수를 2개 작성"

```js
handleChange = e => {
  this.setState({
    [e.target.name]: e.target.value // here!
  });
}

...

render() {
  return (
    <div>
      <input
        type="text"
        name="username" // this!
        (중략)
        onChange={this.handleChange}
      />
      <input
        type="text"
        name="message" //this!
        (중략)
        onChange={this.handleChange}
      />
  )
}
```

### 2. onKeyPress 이벤트 핸들링

ex) `Enter`를 눌렀을 때 `handleClick()` 메소드를 실행할 경우

```js
handleKeyPress = e => {
  if(e.key === 'Enter') {
    this.handleClick();
  }
}
...
    onKeyPress={this.handleKeyPress}
...
```

<br/>

## ref: DOM에 이름달기

- React 프로젝트 내부에서 DOM에 이름을 다는 것
- ref는 id와 달리 전역적으로 작동하지 않고 컴포넌트 내부에서만 작동
- 함수형 컴포넌트에서는 Hook(useRef)을 이용하여 ref를 사용

### 1. DOM을 꼭 사용해야 하는 상황

- (= state만으로 해결할 수 없는 상황 → DOM에 직접 접근)
- 여러 input 중 특정 input에 포커스 주기
- 스크롤 박스 조작하기
- Canvas 요소에 그림 그리기 등

### 2. Callback 함수를 이용한 ref 설정(class type components)

- ref를 달고자 하는 요소에 `ref`라는 callback 함수를 props로 전달
- 이 callback 함수는 ref 값을 파라미터로 전달받음
- 함수 내부에서 파라미터로 받은 ref를 컴포넌트의 멤버 변수로 설정

```js
<input
  ref={(ref) => {
    this.input = ref;
  }}
/>
// 앞으로 this.input은 input 요소의 DOM을 가리킴
// ref의 이름은 자유(ex. this.hello)
```

### 3. createRef를 이용한 ref 설정(class type components)

- React 내장 `createRef` 함수 사용
- React v16.3부터 도입

```js
...
class RefSample extends Component {
  input = React.createRef();

  handleFocus = () => {
    this.input.current.focus(); // callback 사용과 다른 점: 뒷부분의 .current
  }

  render() {
    return(
      <div>
        <input ref = {this.input} />
      </div>
    );
  }
}
...
```

### 4. Component에 ref 달기

- 주로 component 내부에 있는 DOM을 component 외부에서 사용할 때 사용
- DOM에 ref를 다는 방법과 동일

```js
<MyComponent
  ref={(ref) => {
    this.myComponent = ref;
  }}
/>
```

- 내부의 ref에도 접근 가능(ex. `myComponent.handleClick` 등)

<br/>

### 참고) A. onClick={this.myComp.meth} vs B. onClick={()=>this.myComp.meth()}

- A 방법으로 할 경우, 처음 렌더링될 때에는 this.myComp 값이 undefined이므로 `this.myComp.meth`를 읽어오는 과정에서 오류 발생
- 반면, **화살표 함수 문법을 사용하여 아예 새로운 함수를 만들고 그 내부에서 `this.myComp.meth` 메소드를 실행**하면, 버튼을 누를 때(이미 한 번 렌더링을 해서 `this.myComp`를 설정한 시점) `this.myComp.meth` 값을 읽어와서 실행하므로 오류가 발생하지 않음

### 주의)

- 먼저 ref를 사용하지 않고도 원하는 기능을 구현할 수 있는지 반드시 고려한 후 사용
- 컴포넌트끼리 데이터를 교류할 때에는 언제나 데이터를 **부모 ↔ 자식 흐름으로 교류**해야 함 (효율적으로 교류하는 방법: Redux, Context API etc.)
