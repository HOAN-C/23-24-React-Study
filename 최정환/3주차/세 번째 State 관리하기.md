# 세 번째 : State 관리하기

# State를 사용해 Input 다루기

- 선언형 UI : 무엇을 보여주고 싶은지 선언
- 명령형 UI : 발생한 상황에 따른 지침
    - 복잡한 시스템에서는 어려워진다. 새로운 UI나 인터렉션 추가 시 버그 발생 예방을 위해 모든 코드를 봐야 한다.
- UI를 선언적 방식으로 생각하기 위한 과정
    1. 컴포넌트의 다양한 시각적 state를 **확인하세요.**
    2. 무엇이 state 변화를 트리거하는지 **알아내세요.**
    3. `useState`를 사용해서 메모리의 state를 **표현하세요.**
    4. 불필요한 state 변수를 **제거하세요.**
    5. state 설정을 위해 이벤트 핸들러를 **연결하세요.**

<aside>
💡 리액트는 선언형 UI를 사용하며, 이를 위해서는 사용자가 어떤 동작을 기대하는지 생각해봐야 한다!

</aside>

```jsx
import { useState } from 'react';
export default function Picture() {
  const [clicked, setClicked] = useState(false)

  const clickHandler = () => {
    setClicked(!clicked)
  }
  
  return (
    <div className={clicked ? null : "background background--active"} onClick={clickHandler}>
      <img
        className={clicked ? "picture picture--active" : "picture"}
        alt="Rainbow houses in Kampung Pelangi, Indonesia"
        src="https://i.imgur.com/5qwVYb1.jpeg"
        onClick={clickHandler}
      />
    </div>
  );
}
```

# State 구조 선택하기

- 수정과 디버깅이 즐거운 컴포넌트, 버그만 뱉어내는 컴포넌트 차이 :
    - State 구조화
- State 구조화 원칙
    1. **연관된 state 그룹화하기.**
        - 두 개 이상의 state 변수를 항상 동시에 업데이트한다면, 단일 state 변수로 병합하는 것을 고려하세요.
            - (예시로 나온 마우스 포인터 따라가는 기능 신기함.)
    2. **State의 모순 피하기.**
        - 여러 state 조각이 서로 모순되고 “불일치”할 수 있는 방식으로 state를 구성하는 것은 실수가 발생할 여지를 만듭니다. 이를 피하세요.
            - 하나의 목적을 가지는 State 들은 하나로 합치자. state는 boolean이 아니다.
    3. **불필요한 state 피하기.**
        - 렌더링 중에 컴포넌트의 props나 기존 state 변수에서 일부 정보를 계산할 수 있다면, 컴포넌트의 state에 해당 정보를 넣지 않아야 합니다.
            - 내부적 연산을 통해 계산이 가능한 State는 없애자.
    4. **State의 중복 피하기.**
        - 여러 상태 변수 간 또는 중첩된 객체 내에서 동일한 데이터가 중복될 경우 동기화를 유지하기가 어렵습니다. 가능하다면 중복을 줄이세요.
            - 같은 정보를 담고 있는 State는 하나로 합치도록 하자.
    5. **깊게 중첩된 state 피하기.**
        - 깊게 계층화된 state는 업데이트하기 쉽지 않습니다. 가능하면 state를 평탄한 방식으로 구성하는 것이 좋습니다.
            - 데이터가 너무나 중첩되어 있으면 state 변경 시 메모리가 낭비된다. 표현 방식을 다시 생각해보자!

<aside>
💡 State는 매우 매력적인 기능이나, 남발하게 된다면 오히려 머리 아파진다!

</aside>

# 컴포넌트 간 State 공유하기

- 부모 컴포넌트
    - 자식 컴포넌트1
    - 자식 컴포넌트2

위 구조로 되어 있을 때 자식 컴포넌트1, 2에 각 State가 같은 이름으로 선언되어 있어도 다른 State로 취급되며, 각 컴포넌트의 State가 업데이트 될 때는 해당 컴포넌트만 재랜더링 된다.

<aside>
💡 state를 동기화 시키고 싶을 때는 부모 컴포넌트를 찾아 state를 선언하고, 자식 컴포넌트에 props를 통해 전달하면 된다.

</aside>

# State를 보존하고 초기화하기

- state는 컴포넌트 안에 있는 것이 아니라, React 안에 있다.
- 하지만 해당 state가 랜더링되는 유무에 따라 React는 state의 생존 유무를 결정한다.
    - 해당 컴포넌트가 제거된다면 해당 컴포넌트 내에서 사용되던 state는 사라진다.
- React는 JSX 마크업에서가 아닌UI트리에서의 위치에 관심이 있다.
- 같은 위치의 다른 컴포넌트는 State를 초기화한다.
    - 예를 들어, Counter 컴포넌트 대신에  <p> 태그가 랜더링 되었다 Counter 컴포넌트가 다시 랜더링 된다면 해당 컴포넌트 내 state가 초기화 된다.
- 같은 위치에 다른 컴포넌트를 렌더린할 때 컴포넌트는 전체 서브 트리의 state를 초기화 한다.
    - div
        - section
            - Counter(state)
    - 에서
    - div
        - div
            - Counter(state)
    - 로 바꾼다면 Counter state는 초기화 된다.
- 컴포넌트속 컴포넌트가 있다면 부모 컴포넌트가 렌더링 될 때 마다 내부 컴포넌트의 state는 초기화 될 것이다.
    - 따라서 항상 컴포넌트를 중첩해서 정의하지 않고 최상위 범위에서 정의해야 한다.
- 같은 위치에 있는 컴포넌트의 state를 초기화 하는 방법
    1. 다른 컴포넌트 만들기
    2. **key 를 이용하여 폼을 초기화 하기**

<aside>
💡 state는 리액트 안에 존재하지만, 사용되는 컴포넌트의 유무에 따라 초기화 된다.

</aside>

# State 로직을 reducer로 작성하기

- `useState`에서 `useReducer`로 변환 방법:
    1. 이벤트 핸들러에서 action을 전달.
    2. 주어진 state와 action에 대해 다음 state를 반환하는 reducer 함수를 작성.
    3. `useState`를 `useReducer`로 변경.
- reducer를 사용하면 코드를 조금 더 작성해야 하지만 디버깅과 테스트에 도움이 됨.
- reducer는 반드시 순수해야 함.
- 각 action은 단일 사용자 상호작용을 설명해야 함.
- 객체와 배열을 변이하는 스타일로 reducer를 작성하려면 Immer 라이브러리를 사용해야 함.

<aside>
💡 reducer를 사용하면 조금 더 쉽게 state를 사용할 수 있다.

</aside>

### [State를 보존하고 초기화하기] : 주의하세요! 부분 몰?루

# Context를 사용해 데이터를 깊게 전달하기

- Context는 부모 컴포넌트가 그 아래의 트리 전체에 데이터를 전달할 수 있도록 해준다.
- props 드릴링에 대한 대안이 될 수 있다. 멀리 있는 컴포넌트 이더라도 props를 전달할 수 있다.

# Reducer와 Context로 앱 확장하기

- Reducer와 context를 결합해서 컴포넌트가 상위 state를 읽고 수정할 수 있도록 할 수 있습니다.
- State와 dispatch 함수를 하위 컴포넌트들에 제공하는 방법
    1. 두 개의 context를 만듭니다(각각 state와 dispatch 함수를 위한 것).
    2. Reducer를 사용하는 컴포넌트에 두 context를 모두 제공합니다.
    3. 하위 컴포넌트들에서 필요한 context를 사용합니다.
- 더 나아가 하나의 파일로 합쳐서 컴포넌트들을 정리할 수 있습니다.
    - Context를 제공하는 `TasksProvider` 같은 컴포넌트를 내보낼 수 있습니다.
    - 바로 사용할 수 있도록 `useTasks`와 `useTasksDispatch` 같은 사용자 Hook을 내보낼 수 있습니다.
- context-reducer 조합을 앱에 여러 개 만들 수 있습니다.