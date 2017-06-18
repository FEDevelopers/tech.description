> http://www.theodo.fr/blog/2016/03/getting-started-with-react-redux-and-immutable-a-test-driven-tutorial-part-2/ 를 번역한 글입니다.

이 포스트는 React, Redux, Immutable 튜토리얼의 2번째이자 마지막 파트입니다.  
만약 여러분이 첫번째 튜토리얼을 놓쳤다면, 첫번째 파트를 [여기](https://github.com/FEDevelopers/tech.description/wiki/TDD%EA%B8%B0%EB%B0%98-React,Redux,Immutable-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-(1))서 보실 수 있습니다.  

첫번째 포스트에서는 모듈 컴포넌트를 개발하고 유닛 테스팅을 하였고, 앱 UI의 뼈대를 만들었습니다.  

우리는 React props를 이용해서 각 컴포넌트에 앱의 상태를 전달하고, 사용자-액션이 콜백으로 선언 되어짐으로써 UI와 앱 로직을 분리한다는 것을 알 수 있었습니다.  

현재 온보딩 중이거나, 두번째 파트 부터 시작하려고 하면 여기서부터 시작하는 [저장소](https://github.com/phacks/redux-todomvc) 에 관련 커밋을 [여기](https://github.com/phacks/redux-todomvc/commit/aa101fc0f45369ae05f3984715b8213a47d49ddc)서 볼 수 있습니다.  

저장소를 복제하고 이제 따라오세요.!

# Redux 워크플로우 소개
이 시점에서 우리 UI는 상호작용하지 않고 있습니다.: 아이템이 완료로 설정되면, 그 아이템은 줄이 그어질 것이라고 테스트를 했지만, 실제로 아직 사용자가 아이템을 완료할 방법이 없습니다.  
Redux 에코시스템에서 UI 업데이트와 사용자 옵션은 항상 같은 워크플로우를 따릅니다.

- 상태트리는 `props`로 UI와 액션 콜백을 정의합니다.
- 클릭과 같은 사용자 액션은 그것들을 정규화 하는 액션 생성자에게 전달됩니다.
- redux 액션 결과는 실제 앱 로직을 구현한 리듀서로 전달 됩니다.
- 리듀서는 상태트리를 업데이트 하고 디스패치하여 그 상태를 스토어에 저장합니다.
- UI는 스토어에 새로운 상태트리에 따라 업데이트 됩니다.

![리덕스 워크플로우](http://www.theodo.fr/uploads/blog//2016/03/ui_workflow.png)

# 초기 상태 설정
> 참고 : [여기](https://github.com/phacks/redux-todomvc/commit/be48d4d610b3438aeb1dfcd07d317b3c72fbdb3e) 관련 커밋이 있습니다.

첫번째 액션으로 적절하게 Redux 스토어에 초기 상태를 설정 할 수 있습니다.  

Redux 액션은 정보의 *payload* 입니다. 이를테면, 액션이 무엇을 하는지와 앱이 필요하는 기타 정보를 간결하게 설명하는 `type` 속성을 가진 `JSON`객체로 표현합니다.  
아래 같은 경우엔, 타입은 `SET_STATE` 설정 할 수 있다라는 것이며, 우리가 원하는 상태가 포함된 상태 객체를 추가 할 수 있다라는 것입니다.

```javascript
{
  type: 'SET_STATE',
  state: {
    todos: [
      {id: 1, text: 'React', status: 'active', editing: false},
      {id: 2, text: 'Redux', status: 'active', editing: false},
      {id: 3, text: 'Immutable', status: 'active', editing: false},
    ],
    filter: 'all'
  }
}
```

이 액션은 리듀서로 디스패치 될 것이며, 리듀서의 역할을 식별하고 액션과 연관된 실제 로직을 구현 합니다.  
다음의 경우, 로직은 스토어에 새로운 상태를 저장하여 앱을 통해 전달 되게 합니다.  
리듀서 유닛 테스트를 진행보겠습니다.

```
test/reducer_spec.js
```
```javascript
import {List, Map, fromJS} from 'immutable';
import {expect} from 'chai';

import reducer from '../src/reducer';

describe('reducer', ()=>{
  it('handles SET_STATE', ()=>{
    const initialState = Map();
    const action = {
      type: 'SET_STATE',
      state: Map({
        todos : List.of(
          Map({id:1, text:'React', status:'active'}),
          Map({id:2, text:'Redux', status:'active'}),
          Map({id:3, text:'Immutable', status:'completed'})
        )
      })
    };

    const nextState = reducer(initialState, action);

    expect(nextState).to.equal(fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active'},
        {id: 2, text: 'Redux', status: 'active'},
        {id: 3, text: 'Immutable', status: 'completed'}
      ]
    }));
  });
});
```

편의상, 불변 데이터 구조를 사용하는 대신에 일반 javascript로 상태 객체를 작성하고, 리듀서가 변환 처리를 하도록 하고 싶습니다. 마지막으로 리듀서는 `undefined` 인 초기 상태를 우아하게 처리해야 합니다.

```
test/reducer_spec.js
```
```javascript
// ...
describe('reducer', () => {
  // ...
  it('handles SET_STATE with plain JS payload', ()=>{
    const initialState = Map();
    const action = {
      type : 'SET_STATE',
      state : {
        todos: [
          {id: 1, text: 'React', status: 'active'},
          {id: 2, text: 'Redux', status: 'active'},
          {id: 3, text: 'Immutable', status: 'completed'}
        ]
      }
    };

    const nextState = reducer(initialState, action);
    expect(nextState).to.equal(fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active'},
        {id: 2, text: 'Redux', status: 'active'},
        {id: 3, text: 'Immutable', status: 'completed'}
      ]
    }));
  });

  it('handles SET_STATE without initial state', ()=>{
    const action = {
      type : 'SET_STATE',
      state : {
        todos: [
          {id: 1, text: 'React', status: 'active'},
          {id: 2, text: 'Redux', status: 'active'},
          {id: 3, text: 'Immutable', status: 'completed'}
        ]
      }
    };

    const nextState = reducer(undefined, action);
    expect(nextState).to.equal(fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active'},
        {id: 2, text: 'Redux', status: 'active'},
        {id: 3, text: 'Immutable', status: 'completed'}
      ]
    }));
  });
});
```

리듀서는 액션에서 들어온 타입 유형을 매칭하고, 만약 타입이 `SET_STATE`이면, 현재 상태와(우리의 경우 초기상태) 병합될 것 입니다.

```
src/reducer.js
```
```javascript
import {Map} from 'immutable';

function setState(state, newState){
  return state.merge(newState);
}

export default function(state = Map(), action){
    switch(action.type){
      case 'SET_STATE':
        return setState(state, action.state);
    }
    return state;
}
```

앱이 실행될 때, 초기 상태가 우리 액션으로 설정하기 위해 우리는 앱과 리듀서를 연결 해야 합니다.  
사실 우린 실제 Redux 라이브러리를 처음 사용하기 때문에, Redux를 설치 해야합니다.

```
npm install --save redux@3.3.1 react-redux@4.4.1
```

```
src/index.jsx
```
```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import {List, Map} from 'immutable';
import {createStore} from 'redux';
import {Provider} from 'react-redux';
import reducer from './reducer';
import {TodoAppContainer} from './components/TodoApp';

// 새로운 Redux 스토어 초기화
const store = createStore(reducer);
// 원하는 상태로 유지하기 위해 SET_STATE 디스패치
store.dispatch({
  type: 'SET_STATE',
  state: {
    todos: [
      {id: 1, text: 'React', status: 'active', editing: false},
      {id: 2, text: 'Redux', status: 'active', editing: false},
      {id: 3, text: 'Immutable', status: 'active', editing: false},
    ],
    filter: 'all'
  }
});

require('../node_modules/todomvc-app-css/index.css');

ReactDOM.render(
  // 컴포넌트로 스토어를 전달하기 위한 Provider 컴포넌트를 랩핑합니다.
  <Provider store={store}>
    <TodoAppContainer />
  </Provider>,
  document.getElementById('app')
);
```

앞선 코드스니펫을 잘 살펴보면, `TodoApp` 컴포넌트가 `TodoAppContainer` 컴포넌트로 대체 된 것을 알 수 있습니다. Redux에는 `Presentational`과 `Container` 라는 두가지 타입의 컴포넌트가 있습니다. Dan Abramov의 [highly informative article](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.e5z2vws8e) 기사를 보면 두 타입의 차이점을 알 수 있습니다.  

빠르게 Redux 문서를 인용하여 요약 해드리겠습니다.  

`Presentational` 은 어떻게 보이는지(스타일, 템플릿), 그리고 `Container` 는 어떻게 작동하는지(데이터 패칭, 상태 업데이트) 에 관한 것입니다.  

자 이제 우리는 스토어를 설정 하였고, 스토어를 `TodoAppContainer` 컴포넌트에 전달 하였습니다. 하지만 자식 컴포넌트가 스토어를 이해 하기 위해선, `TodoApp` 컴포넌트의 React `props` 와 상태 속성을 매핑 해야 합니다. 이 작업이 `TodoAppContainer` 에게 주는 행위 입니다.

```
src/components/TodoApp.jsx
```
```javascript
// ...
import {connect} from 'react-redux';

export class TodoApp extends React.Component{
  // ...
}

function mapStateToProps(state){
  return {
    todos : state.get('todos'),
    filter : state.get('filter')
  };
}

export const TodoAppContainer = connect(mapStateToProps)(TodoApp);
```

브라우저에서 앱을 리로드 하게되면, Redux 툴을 사용하지 않는 이상 이전과 같이 초기화 된 상태를 보게 될 것 입니다.

# Redux dev tools
> 참고 : [여기](https://github.com/phacks/redux-todomvc/commit/9e82a2bf7ffaea5d0fda6af361a126517aecc115)에 관련 커밋이 있습니다.

Redux 스토어와 리듀서를 설정했으므로, 이제 우리는 간소화된 개발을 위해 `Redux dev tools` 을 설정 하겠습니다.  
첫번째로, [Redux 개발도구 크롬확장프로그램](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd) 으로 가서 설치 하세요.  
개발 도구는 스토어 생성시에 활성화 됩니다.

```
src/index.jsx
```
```javascript
// ...
import {compose, createStore} from 'redux';

const createStoreDevTools = compose(
  window.devToolsExtension ? window.devToolsExtension() : f => f
)(createStore);
const store = createStoreDevTools(reducer);
// ...
```

![dev tools](http://www.theodo.fr/uploads/blog//2016/03/redux-dev-tools.png)

브라우저에서 앱을 리로드하고 개발도구에서 Redux 아이콘을 클릭하면 보여집니다.

`Diff Monitor`, `Log Monitor`, `Slider Monitor` 의 3가지 다른 모니터링을 바로 사용 할 수 있습니다.

# 액션 생성자로 액션 설정
## 아이템 상태 토글
> 참고 : [여기](https://github.com/phacks/redux-todomvc/commit/7a2dc0963684b569c11f92e41a324324dfb21bdc)에 관련 커밋이 있습니다.

다음 단계로 `todos` 아이템의 활성화와 완료 상태 사이를 사용자가 토글 할 수 있게 해줄 것입니다.  

첫번째로, 리듀서는 활성화와 완료 상태 사이를 변경하는 새로운 액션인 `TOGGLE_COMPLETE` 를 처리 해야합니다.

```
test/reducer_spec.js
```
```javascript
import {List, Map, fromJS} from 'immutable';
import {expect} from 'chai';

import reducer from '../src/reducer';

describe('reducer', () => {
  // ...

  it('handles TOGGLE_COMPLETE by changing the status from active to completed', ()=>{
    const initialState = fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active'},
        {id: 2, text: 'Redux', status: 'active'},
        {id: 3, text: 'Immutable', status: 'completed'}
      ]
    });

    const action = {
      type: 'TOGGLE_COMPLETE',
      itemId: 1
    }

    const nextState = reducer(initialState, action);
    expect(nextState).to.equal(fromJS({
      todos: [
        {id: 1, text: 'React', status: 'completed'},
        {id: 2, text: 'Redux', status: 'active'},
        {id: 3, text: 'Immutable', status: 'completed'}
      ]
    }));
  });

  it('handles TOGGLE_COMPLETE by changing the status from completed to active', ()=>{
    const initialState = fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active'},
        {id: 2, text: 'Redux', status: 'active'},
        {id: 3, text: 'Immutable', status: 'completed'}
      ]
    });
    const action = {
      type: 'TOGGLE_COMPLETE',
      itemId : 3
    }

    const nextState = reducer(initialState, action);
    expect(nextState).to.equal(fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active'},
        {id: 2, text: 'Redux', status: 'active'},
        {id: 3, text: 'Immutable', status: 'active'}
      ]
    }));
  });
});
```

테스트를 통과하기 위해 리듀서를 업데이트 하겠습니다.

```
src/reducer.js
```
```javascript
// ...
function toggleComplete(state, itemId){
  // 아이템 ID로 할당된 index 찾기
  const itemIndex = state.get('todos').findIndex(
    (item) => item.get('id') === itemId;
  );

  // index 로 todo를 업데이트
  const updatedItem = state.get('todos')
    .get(itemIndex)
    .update('status', status => status === 'active' ? 'completed' : 'active');

  // 수정된 todo아이템으로 상태를 업데이트
  return state.update('todos', todos => todos.set(itemIndex, updatedItem));
}

export default function(state=Map(), action){
  switch (action.type) {
    case 'SET_STATE':
      return setState(state, action.state);
    case 'TOGGLE_COMPLETE':
      return toggleComplete(state, action.itemId);
  }
  return state;
}
```

우리는 `SET_STATE` 액션과 같은 선상에서, `TodoAppContainer` 컴포넌트도 이 액션을 인식 시키도록 만들어야 합니다. 따라서 `toggleComplete` 콜백은 `TodoItem` 컴포넌트로(실제 호출하는 컴포넌트) 전달 됩니다.

Redux에는 이를 진행 하기 위한 표준 방법으로 `액션 생성자(Action Creators)`가 있습니다.  
액션 생성자는 적절하게 포맷팅된 액션을 반환하는 간단한 함수 입니다. - 이 함수는 React props와 매핑되어집니다.  

이제 첫번째 액션 생성자를 만들어 보겠습니다.

```
src/action_creators.js
```
```javascript
export function toggleComplete(itemId){
  return {
    type: 'TOGGLE_COMPLETE',
    itemId
  }
}
```

이제 스토어 데이터를 가져오는데 사용했던  `TodoAppContainer` 컴포넌트 안에 `connect`함수를 호출 합니다. 그리고 `props` 콜백을 액션 생성자의 같은 이름과 매핑 하도록 지시합니다.

```
src/components/TodoApp.jsx
```
```javascript
// ...
import * as actionCreators from '../action_creators';
export class TodoApp extends React.Component{
  // ..
  render() {
    return <div>
      // ...
        // spread연산자로 넘기기
        <TodoList {...this.props} />
      // ...
      </div>
  }
};

export const TodoAppContainer = connect(mapStateToProps, actionCreators)(TodoApp);
```

이제 웹서버와 브라우저를 재시작 해보세요. 이제 아이템을 클릭하면 상태가 적절하게 토글됩니다. 그리고 Redux 개발툴을 보면, 액션이 트리거 되는것과 후속 업데이트 되는 상태를 볼 수 있습니다.

##현재 필터 변경
> [여기](https://github.com/phacks/redux-todomvc/commit/3949d4f38912e4b6b8e60fc4c553614d4076028c)서 다음 관련 커밋을 볼 수 있습니다.

모든 세팅이 완료 되었으니, 다른 액션을 작성하는 것은 쉽습니다. 이제 우리는 `CHANGE_FILTER` 액션을 작성할 것 입니다. 아마 추측할 수 있을텐데, 현재 상태 필터를 변경하면 표시되는 아이템이 필터링 되는 것입니다.  

액션 생성자를 작성해보도록 하겠습니다.

```
src/action_creators.js
```
```javascript
// ...
export function changeFilter(filter){
  return {
    type: 'CHANGE_FILTER',
    filter
  }
}
```

이제 리듀서용 유닛테스트를 작성합니다.

```
test/reducer_spec.js
```
```javascript
// ...
describe('reducer', () => {
  // ...
  it('les CHANGE_FILTER by changing the filte', () =>{
    const initialState = fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active'},
      ],
      filter: 'all'
    });
    const action = {
      type: 'CHANGE_FILTER',
      filter: 'active'
    }
    const nextState = reducer(initialState, action);
    expect(nextState).to.equal(fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active'},
      ],
      filter: 'active'
    }));
  });
});
```

이제 리듀서 함수를 작성하겠습니다.

```
src/reducer.js
```
```javascript
// ...
function changeFilter(state, filter){
  return state.set('filter', filter);
}
export default function(state=Map(), action){
  switch(action.type){
    case 'SET_STATE':
      return setState(state, action.state);
    case 'TOGGLE_COMPLETE':
      return toggleComplete(state, action.itemId);
    case 'CHANGE_FILTER':
      return changeFilter(state, action.filter);
  }
  return state;
}
```

마지막으로, 우리는 `changeFilter` 콜백을 `TodoTools` 컴포넌트로 전달해야 합니다.

```
TodoApp.jsx
```
```javascript
// ...
export class TodoApp extends React.Component{
  // ..
  render() {
    return <div>
      <section className="todoapp">
        // ...
        <TodoTools changeFilter={this.props.changeFilter}
                    filter={this.props.filter}
                    nbActiveItems={this.getNbActiveItems()} />
      </section>
      <Footer />
    </div>
  }
};
```

이게 끝입니다. 필터가 완벽하게 작동할 것입니다.

## 아이템 수정
> [여기](https://github.com/phacks/redux-todomvc/commit/2a7b1138f778524d4aa7eac193c995258d28c5a3) 관련 커밋이 있습니다.

사용자가 아이템을 수정할 때, 실제로는 3가지 중 2가지 액션이 트리거된 작업이 있습니다.

- 사용자가 엔터를 치면 수정모드로 돌입: `EDIT_ITEM`;
- 사용자가 저장하지 않은채 수정모드를 취소할 때: `CANCEL_EDITING`;
- 사용자가 저장하고 유효성 검사: `DONE_EDITING`;

이제 위 3개의 액션을 위한 액션 생성자를 작성 하겠습니다.

```
src/action_creators.js
```
```javascript
// ...
export function editItem(itemId){
  return {
    type: 'EDIT_ITEM',
    itemId
  }
}

export function cancelEditing(itemId){
  return {
    type: 'CANCEL_EDITING',
    itemId
  }
}

export function doneEditing(itemId, newText){
  return {
    type: 'DONE_EDITING',
    itemId,
    newText
  }
}
```

이제 각 액션을 위한 유닛테스트를 작성하겠습니다.

```
test/reducer_spec.js
```
```javascript
// ...
describe('reducer', ()=>{
  // ...
  it('handles EDIT_ITEM by setting editing to true',()=>{
    const initialState = fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active', editing: false},
      ]
    });
    const action = {
      type: 'EDIT_ITEM',
      itemId: 1
    }
    const nextState = reducer(initialState, action);
    expect(nextState).to.equal(fromJS({
      todos:[
         {id: 1, text: 'React', status: 'active', editing: true},
      ]
    }));
  });

  it('handles CANCEL_EDITING by setting editing to false', ()=>{
    const initialState = fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active', editing: true},
      ]
    });
    const action = {
      type: 'CANCEL_EDITING',
      itemId: 1
    }
    const nextState = reducer(initialState, action);
    expect(nextState).to.equal(fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active', editing: false},
      ]
    }));
  });

  it('handles DONE_EDITING by setting by updating the text', ()=>{
    const initialState = fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active', editing: true},
      ]
    });
    const action = {
      type: 'DONE_EDITING',
      itemId: 1,
      newText: 'Redux'
    }
    const nextState = reducer(initialState, action);
    expect(nextState).to.equal(fromJS({
      todos: [
        {id: 1, text: 'Redux', status: 'active', editing: false},
      ]
    }));
  });
});
```

이제 우리는 3가지 액션을 실제로 다룰 수 있는 리듀서 함수를 개발 할 수 있습니다.

```
src/reducer.js
```
```javascript
function findItemIndex(state, itemId){
  return state.get('todos').findIndex(
    (item) => item.get('id') === itemId
  );
}

// findItemIndex 사용하기 위해 toggleComplete 함수를 리팩토링 합니다.
function toggleComplete(state, itemId){
  const itemIndex = findItemIndex(state, itemId);
  const updatedItem = state.get('todos')
    .get(itemIndex)
    .update('status', status => status === 'active' ? 'completed' : 'active');

  return state.update('todos', todos => todos.set(itemIndex, updatedItem));
}

function editItem(state, itemId){
  const itemIndex = findItemIndex(state, itemId);
  const updatedItem = state.get('todos')
    .get(itemIndex)
    .set('editing', true);

  return state.update('todos', todos => todos.set(itemIndex, updatedItem));
}

function cancelEditing(state, itemId){
  const itemIndex = findItemIndex(state, itemId);
  const updatedItem = state.get('todos')
    .get(itemIndex)
    .set('editing', false);

  return state.update('todos', todos => todos.set(itemIndex, updatedItem));
}

function doneEditing(state, itemId, newText){
  const itemIndex = findItemIndex(state, itemId);
  const updatedItem = state.get('todos')
    .get(itemIndex)
    .set('editing', false)
    .set('text', newText)

  return state.update('todos', todos => todos.set(itemIndex, updatedItem));
}

export default function(state=Map(), action){
  switch(action.type){
    // ...
    case 'EDIT_ITEM':
      return editItem(state, action.itemId);
    case 'CANCEL_EDITING':
      return cancelEditing(state, action.itemId);
    case 'DONE_EDITING':
      return doneEditing(state, action.itemId, action.newText);
  }
  return state;
}
```

이제 브라우저에서 잘 작동 할 것입니다.

## 완료 삭제와 아이템 추가 삭제
> [여기](https://github.com/phacks/redux-todomvc/commit/c89059a6767903fdf8b9827209f92e1f7385bdb7) 관련 커밋이 있습니다.

나머지 3개 액션은 다음과 같습니다.

- `CLEAR_COMPLETED`, `TodoTools` 컴포넌트에서 트리거되며, 리스트에 완료된 아이템을 없애줍니다.
- `ADD_ITEM`, `TodoHeader` 컴포넌트에서 트리거되며, 사용자가 엔터친 텍스트로 된 아이템이 추가됩니다.
- `DELETE_ITEM`, `TodoItem`에서 호출되며, 아이템을 삭제합니다.

우리는 이제 액션 생성자 추가, 리듀서 유닛데스트와 코드로직, `props`로 콜백을 전달하는 이벤트같은 워크플로우를 사용하겠습니다.

```
src/action_creators.js
```
```javascript
// ...
export function clearCompleted(){
  return {
    type : 'CLEAR_COMPLETED'
  }
}

 export function addItem(text){
  return {
    type: 'ADD_ITEM',
    text
  }
 }

 export function deleteItem(itemId){
  return {
    type: 'DELETE_ITEM',
    itemId
  }
 }
```

```
test/reducer_spec.js
```
```javascript
// ...
describe('reducer', () => {
  // ... 
  it('handles CLEAR_COMPLETED by removing all the completed items', ()=>{
    const initialState = fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active'},
        {id: 2, text: 'Redux', status: 'completed'},
      ]
    });

    const action = {
      type: 'CLEAR_COMPLETED'
    }

    const nextState = reducer(initialState, action);
    expect(nextState).to.equal(fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active'},
      ]
    }));
  });

  it('handles ADD_ITEM by adding the item', () => {
    const initialState = fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active'}
      ]
    });

    const action = {
      type: 'ADD_ITEM',
      text: 'Redux'
    }

    const nextState = reducer(initialState, action);
    expect(nextState).to.equal(fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active'},
        {id: 2, text: 'Redux', status: 'active'},
      ]
    }));
  });

  it('handles DELETE_ITEM by removing the item', () => {
    const initialState = fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active'},
        {id: 2, text: 'Redux', status: 'completed'},
      ]
    });

    const action = {
      type: 'DELETE_ITEM',
      itemId: 2
    }

    const nextState = reducer(initialState, action);
    expect(nextState).to.equal(fromJS({
      todos: [
        {id: 1, text: 'React', status: 'active'},
      ]
    }));
  });
});
```

```
src/reducer.js
```
```javascript
function clearCompleted(state){
  return state.update('todos',
    (todos) => todos.filterNot(
      (item) => item.get('status') === 'completed'
    )
  );
}

function addItem(state, text){
  const itemId = state.get('todos').reduce((maxId, item) => Math.max(maxId, item.get('id')), 0) + 1;
  const newItem = Map({id: itemId, text: text, status: 'active'});
  return state.update('todos', (todos) => todos.push(newItem));
}

function deleteItem(state, itemId){
  return state.update('todos',
    (todos) => todos.filterNot(
      (item) => item.get('id') === itemId
    )
  );
}

export default function(state = Map(), action){
  switch(action.type){
    // ...
    case 'CLEAR_COMPLETED':
      return clearCompleted(satate);
    case 'ADD_ITEM':
      return addItem(state, action.text);
    case 'DELETE_ITEM':
      return deleteItem(state, action.itemId);
  }
  return state;
}
```

```
src/components/TodoApp.jsx
```
```javascript
// ...
export class TodoApp extends React.Component{
  // ...
  render() {
    return <div>
      <section className="todoapp">
        // addItem 콜백을 전달
        <TodoHeader addItem={this.props.addItem}/>
        <TodoList {...this.props} />
        // clearCompleted 콜백 전달
        <TodoTools changeFilter={this.props.changeFilter}
                    filter={this.props.filter}
                    nbActiveItems={this.getNbActiveItems()}
                    clearCompleted={this.props.clearCompleted} />
      </section>
      <Footer />
    </div>
  }
};
```

와우 `TodoMVC` 앱이 이제 완성되었습니다.

# 마무리
React, Redux, Immutable 스택 TDD 튜토리얼은 마쳤습니다.  

더 많은 것들을 알고 싶어하는 여러분들을 위해 다음과 같은 것들이 있습니다.

- [React Redux router](https://github.com/reactjs/react-router-redux) SPA를 완벽하게 구축할 수 있습니다.
- 백엔드에서 Redux를 사용하여 `Isomorphic Redux`은 [2개](https://blog.diacode.com/trello-clone-with-phoenix-and-react-pt-1) [튜토리얼](http://teropa.info/blog/2015/09/10/full-stack-redux-tutorial.html) 에서 광범위하게 다뤄지고있습니다.
- [Gambit](https://github.com/Ghirro/gambit)은 API와 간단하게 연결 하게 해주는 작은 Redux 래퍼입니다.
- Dan Abramov(redux 창시자)이 만든 [무료 비디오영상](https://egghead.io/series/getting-started-with-redux)은  깊이있는 Redux에 대한 내용을 다루고 있습니다.
- [Redux 웹사이트](http://redux.js.org/docs/introduction/Ecosystem.html)에서 더 많은 것을 이용할 수 있습니다.