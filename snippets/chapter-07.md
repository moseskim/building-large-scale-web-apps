7 상태 관리
===

- `useState` 훅

```javascript
function HelloWorld() {
  const [message, setMessage] = React.useState(
    "Hello World!",
  );

  return (
    <div>
      {/* "message" 값을 표시한다. */}
      <h1>{message}</h1>

      {/*
        버튼을 클릭하면 "message" 상태는 업데이트되고
        컴포넌트는 재 렌더링을 해서 새로운 "message" 값을
        표시한다.
      */}
      <button
        onClick={() =>
          setMessage("Hello React!")
        }
      >
        Change Message
      </button>
    </div>
  );
}
```

## 7.1. 컴포넌트 간 데이터 관리하기

- 마크업 안에서 props를 받아 사용하는 HelloWorld 컴포넌트

```javascript
function HelloWorld(props) {
  return (
    <div>
      <h1>{props.message}</h1>
      <button onClick={props.onChangeMessage}>
        Change Message
      </button>
    </div>
  );
}
```

- 리액트에서 props를 사용해 콜백 함수를 아래로 전달하기(부모 컴포넌트)

```javascript
function App() {
  const [message, setMessage] = useState(
    "Hello World!",
  );

  const changeMessage = () => {
    setMessage("Hello React!");
  };

  // onChangeMessage()는 props로서 아래로 전달된다.
  return (
    <HelloWorld
      message={message}
      onChangeMessage={changeMessage}
    />
  );
}
```

- 리액트에서 props를 사용해 콜백 함수를 아래로 전달하기(자식 컴포넌트)

```javascript
function HelloWorld(props) {
  return (
    <div>
      <h1>{props.message}</h1>

      {/*
        onChangeMessage() 함수는 부모에게서 왔다.
      */}
      <button onClick={props.onChangeMessage}>
        Change Message
      </button>
    </div>
  );
}
```

## 7.2. Prop 내려보내기

- 리액트에서 컨텍스트를 사용해 데이터 전달하기(부모 컴포넌트)

```javascript
import React, {
  useState,
  createContext,
  useContext,
} from "react";

// 컨텍스트를 만든다.
const MessageContext = createContext();

function App() {
  const [message, setMessage] = useState(
    "Hello World!",
  );

  return (
    // 중첩된 컴포넌트들에게 상태를 제공한다.
    <MessageContext.Provider
      value={{ message, setMessage }}
    >
      <Child1>
        <Child2>
          <Child3>
            <DeeplyNestedChild />
          </Child3>
        </Child2>
      </Child1>
    </MessageContext.Provider>
  );
}
```

- 리액트에서 컨텍스트를 사용해 데이터 전달하기(깊이 중첩된 자식 컴포넌트)

```javascript
function DeeplyNestedChild() {
  /*
    데이터를 prop으로 받지 않고 직접 사용한다.
  */
  const { message, setMessage } = useContext(
    MessageContext,
  );
  
  return (
    <div>
      <h1>{message}</h1>
      <button
        onClick={() =>
          setMessage(
            "Hello from nested component!",
          )
        }
      >
        Change Message
      </button>
    </div>
  );
}
```

## 7.3. 단순한 상태 관리

- `useReducer` 훅

```javascript
import React, { useReducer } from "react";

const initialState = {
  message: "Hello World!",
};

function reducer(state, action) {
  switch (action.type) {
    case "CHANGE_MESSAGE":
      return {
      ...state,
      message: action.payload,
      };
    default:
      throw new Error();
  }
}

function App() {
  const [state, dispatch] = useReducer(
    reducer,
    initialState,
  );

  return (
    <div>
      <h1>{state.message}</h1>
      <button
        onClick={() =>
          dispatch({
            type: "CHANGE_MESSAGE",
            payload: "New Message!",
          })
        }
      >
        Change Message
      </button>
    </div>
  );
}
```

## 7.4. 상태 관리 전용 라이브러리

- 리덕스와 리덕스 툴킷 설치하기

```shell
yarn add @reduxjs/toolkit react-redux
```

- `store.js` 파일

```javascript
import {
  createSlice,
  configureStore,
} from "@reduxjs/toolkit";

const initialState = {
  message: "Hello World!",
};

export const messageSlice = createSlice({
  name: "message",
  initialState,
  reducers: {
    changeMessage: (state, action) => {
      state.message = action.payload;
    },
  },
});

export const store = configureStore({
  reducer: messageSlice.reducer,
});
```

- 리덕스 스토어를 모든 컴포넌트들이 사용할 수 있게 만들기

```javascript
import { createRoot } from "react-dom/client";
import { Provider } from "react-redux";

import { App } from "./App";
import { store } from "./store";

const rootElement =
  document.getElementById("root");
const root = createRoot(rootElement);

root.render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

- 리액트 컴포넌트로부터 리덕스 상태 관리하기

```javascript
import React from "react";
import {
  useSelector,
  useDispatch,
} from "react-redux";

import { messageSlice } from "./store";

export function App() {
  const message = useSelector(
    (state) => state.message,
  );
  const dispatch = useDispatch();
  
  const handleChangeMessage = () => {
    dispatch(
      messageSlice.actions.changeMessage(
        "New Redux Message!",
      ),
    );
  };

  return (
    <div>
      <h1>{message}</h1>
      <button onClick={handleChangeMessage}>
        Change Message
      </button>
    </div>
  );
}

export default App;
```
