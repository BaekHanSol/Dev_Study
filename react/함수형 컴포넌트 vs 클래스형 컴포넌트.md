
**컴포넌트란?**
- 사용자 인터페이스를 대표하는 것
- 컴포넌트는 재사용할 수 있고 사용자 인터페이스에서 어떤 곳이든지 사용할 수 있다.

**함수형 컴포넌트(Stateless Component)**
- 단순히 html 사용자 인터페이스를 return하는 javascript 함수
- 데이터를 받고 해당 데이터를 렌더링하는 UI에 표시하는 역할을 수행한다.
- 함수에서 properties를 받고 html(jsx)을 return할 수 있다.
- state를 사용하지 않을 수 있다.
- 라이프 사이클
  - 함수형 혹은 hook을 사용.
  - 컴포넌트 라이프 사이클 함수 사용x(함수형 컴포넌트는 단순히 javascript function이다.)
  -> state, setState 대신 Hooks를 사용한다.(useEffect)
- Redux를 사용하여 컴포넌트를 구성할 때 Presentational 컴포넌트인 경우 함수형 컴포넌트를 사용하는 것이 좋다.

**클래스형 컴포넌트(Stateful Component)**
- 대부분 Component를 상속받음
- 무조건 render() 함수 있어야 함
- Redux를 사용하여 컴포넌트를 구성할 때 Container Component를 사용

현재까지 함수형 컴포넌트와 클래스형 컴포넌트를 사용하면서 성능차이를 느끼진 못했다. 개인적으론 라이프사이클 API를 제공하는 클래스형 컴포넌트를 선호하는 편이다.
