18 리액트의 미래
=====

## 18.2. 새로운 훅과 API

- 비동기 폼 제출 처리를 위한 간단한 컴포넌트

```javascript
import React, { useState, useCallback } from 'react'

const submitForm = async () => {/* 폼 제출 */}

export function Component() {
  // 폼 상태를 생성한다.
  const [formState, setFormState] = useState(null)
  const [isPending, setIsPending] = useState(false)

  // 폼 제출을 처리한다.
  const formAction = useCallback(async (event) => {
    // ...
  }, [])

  // 폼 템플릿을 표시한다.
  return (
    <form onSubmit={formAction}>
      {/* 폼 템플릿 */}
    </form>
  )
}
```

- 폼이 제출되었을 때 상태 변경 처리하기

```javascript
import React, { useState, useCallback } from "react";

const submitForm = async () => {
  /* 폼을 제출한다. */
};

export function Component() {
  // 폼 상태를 생성한다.
  const [formState, setFormState] = useState(null);
  const [isPending, setIsPending] = useState(false);

  // 폼 제출을 처리한다.
  const formAction = useCallback(async (event) => {
    event.preventDefault();
    setIsPending(true);
    try {
      const result = await submitForm();
      setFormState(result);
    } catch (error) {
      setFormState({
        message: "Failed to complete action",
      });
    }
    setIsPending(false);
  }, []);

  // 폼 템플릿을 표시한다.
  return (
    <form onSubmit={formAction}>
      {/* 폼 템플릿 */}
    </form>
  );
}
```

- `useTransition` 훅을 사용해 비동기 업데이트 처리하기

```javascript
import { useState, useTransition } from "react";

const submitForm = async () => {
  /* 폼을 제출한다. */
};

export function Component() {
  const [formState, setFormState] = useState(null);
  const [isPending, startTransition] =
    useTransition();

  const formAction = (event) => {
    event.preventDefault();

    startTransition(async () => {
      try {
        const result = await submitForm();
        setFormState(result);
      } catch (error) {
        setFormState({
          message: "Failed to complete action",
        });
      }
    });
  };

  return (
    <form onSubmit={formAction}>
      {/* 폼 입력 */}

      {/* ... */}

      {isPending ? <h4>Pending...</h4> : null}
      {formState?.message && (
        <h4>{formState.message}</h4>
      )}
    </form>
  );
}
```

- `useActionState` 훅 시그니처

```javascript
import { useActionState } from "react";

export function Component() {
  const [state, dispatch, isPending] = useActionState(
    action,
    initialState,
    permalink,
  );

  // ...
}
```

- 폼 제출을 처리하는 액션 함수 만들기

```javascript
import { useActionState } from "react";

const submitForm = async () => {
  /* 폼을 제출한다. */
};

const action = async (currentState, formData) => {
  try {
    const result = await submitForm(formData);
    return { message: result };
  } catch {
    return { message: "Failed to complete action" };
  }
};

export function Component() {
  const [state, dispatch, isPending] = useActionState(
    action,
    null
  );

  // ...
}
```

- `dispatch`를 `<form>` 액션 prop에 전달하기

```javascript
import { useActionState } from "react";

const submitForm = async () => {
  /* 폼을 제출한다. */
};

const action = async (currentState, formData) => {
  try {
    const result = await submitForm(formData);

    return { message: result };
  } catch {
    return { message: "Failed to complete action" };
  }
};

export function Component() {
  const [state, dispatch, isPending] = useActionState(
    action,
    null
  );

  return (
    <form action={dispatch}>
      {/* ... */}
    </form>
  )
}
```

- 템플릿에서 폼 `state`와 `isPending` 상태를 표시하기

```javascript
import { useActionState } from "react";

const submitForm = async () => {
  /* 폼을 제출한다. */
};

const action = async (currentState, formData) => {
  try {
    const result = await submitForm(formData);

    return { message: result };
  } catch {
    return { message: "Failed to complete action" };
  }
};

export function Component() {
  const [state, dispatch, isPending] = useActionState(
    action,
    null,
  );

  return (
    <form action={dispatch}>
      <input
        type="text"
        name="text"
        disabled={isPending}
      />

      <button type="submit" disabled={isPending}>
        Add Todo
      </button>

      {/*
        폼 제출이 성공 혹은 실패했을 때 전달할 폼 상태 메시지를 출력한다.
      */}
      {state.message && <h4>{state.message}</h4>}
    </form>
  );
}
```

- `useFormStatus` 훅을 사용해 부포 폼 제출 상태에 접근하기

```javascript
import { useFormStatus } from "react";

export function NestedComponent() {
  /* 마지막 폼 제출 정보에 접근한다. */
  const { pending, data, method, action } =
    useFormStatus();

    return (
      /* ... */
    )
}
```

- 폼 제출 이후 `message` prop을 업데이트하는 컴포넌트

```javascript
import React, { useState } from "react";

export function Component({
  message,
  updateMessage,
}) {
  const [inputMessage, setInputMessage] =
    useState("");

  const submitForm = async (event) => {
    event.preventDefault();
    const newMessage = inputMessage;

    // API 제출이 해결되면 상태를 업데이트한다.
    const updatedMessage =
      await submitToAPI(newMessage);

    updateMessage(updatedMessage);
  };

  return (
    <form onSubmit={submitForm}>
      {/* 현재 메시지를 보여준다. */}
      <p>{message}</p>

      <input
        type="text"
        name="text"
        value={inputMessage}
        onChange={(e) =>
          setInputMessage(e.target.value)
        }
      />
      <button type="submit">Add Message</button>
    </form>
  );
}
```

- 비동기 폼 제출을 하는 동안 실시간으로 템플릿을 낙관적 업데이트하기

```javascript
import React, {
  useState,
  useOptimistic,
} from "react";

export function Component({
  message,
  updateMessage,
}) {
  const [inputMessage, setInputMessage] =
    useState("");

  const [
    optimisticMessage,
    setOptimisticMessage,
  ] = useOptimistic(message);

  const submitForm = async (event) => {
    event.preventDefault();
    const newMessage = inputMessage;

    /* API 변경을 트리거하기 전에,
       낙관적으로 값을 설정한다. */
    setOptimisticMessage(newMessage);

    // API 제출이 해결되면 상태를 업데이트한다.
    const updatedMessage =
      await submitToAPI(newMessage);
    updateMessage(updatedMessage);
  };

  return (
    <form onSubmit={submitForm}>
      {/* 낙관적 값을 보여준다. */}
      <p>{optimisticMessage}</p>

      <input
        type="text"
        name="text"
        value={inputMessage}
        onChange={(e) =>
          setInputMessage(e.target.value)
        }
      />
      <button type="submit">Add Message</button>
    </form>
    );
}
```

- 리액트에서 컨텍스를 사용해 데이터 전달하기(부모 컴포넌트)

```javascript
import React, {
  useState,
  createContext,
} from "react";

// 컨텍스트를 생성한다.
const MessageContext = createContext();

function App() {
  const [message, setMessage] = useState(
    "Hello World!",
  );

  return (
  // 중첩된 컴포넌트들에게 상태를 제공한다.
    <MessageContext
      value={{ message, setMessage }}
    >
      <Child1>
        <Child2>
          <Child3>
            <DeeplyNestedChild />
          </Child3>
        </Child2>
      </Child1>
    </MessageContext>
  );
}
```

- `use`를 사용해 컨텍스트 읽기(자식 컴포넌트)

```javascript
import { use } from "react";
import { MessageContext } from "./context";

function DeeplyNestedChild() {
  // 새로운 use() API를 사용해 데이터에 접근한다.
  const { message, setMessage } = use(
    MessageContext
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

- 조건 안에서 컨텍스트 읽기(자식 컴포넌트)

```javascript
import { use } from "react";
import { MessageContext } from "./context";

function DeeplyNestedChild() {
  // 새로운 use() API를 사용해서 데이터에 접근한다.
  const { message, setMessage } = use(
    MessageContext
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

## 18.3. 리액트 컴파일러

- `TodoList` 리액트 컴포넌트

```javascript
import React, {
  useState,
  useEffect,
} from "react";

const TodoList = () => {
  const [todos, setTodos] = useState([]);

  useEffect(() => {
    const fetchTodos = async () => {
      try {
        const response = await fetch(
          "https://dummyjson.com/todos",
        );
        const data = await response.json();
        setTodos(data.todos);
      } catch (error) {
        console.error(
          "Error fetching todos:",
          error,
        );
      }
    };

    fetchTodos();
  }, []);

  return (
    <div>
      <ul>
        {todos.map((todo) => (
          <li key={todo.id}>{todo.todo}</li>
        ))}
      </ul>
    </div>
  );
};

export default TodoList;
```

- `useEffect`의 빈 의존성 배열

```javascript
useEffect(() => {
  const fetchTodos = async () => {
    // ...
  };

  fetchTodos();
  /*
    빈 의존성 배열은 이 효과가 컴포넌트가 마운트된 뒤
    단 한 번만 실행되는 것을 보장한다.
  */
}, []);
```

- `React.memo`를 사용해 컴포넌트 함수를 메모화하기

```javascript
import React, {
  useState,
  useEffect,
  memo,
} from "react";

const TodoItem = memo(({ todo }) => {
  return <li>{todo}</li>;
});

const TodoList = () => {
  // ...
  // ...

  return (
    <div>
      <ul>
        {todos.map((todo) => (
          <TodoItem
            key={todo.id}
            todo={todo.todo}
          />
        ))}
      </ul>
    </div>
  );
};

export default TodoList;
```

- `useMemo` 훅을 사용해 컴포넌트 값 메모화하기

```javascript
import React, {
  useState,
  useEffect,
  useMemo,
} from "react";

const TodoList = () => {
  const [todos, setTodos] = useState([]);

  // ...
  // ...

  const completedCount = useMemo(() => {
    return todos.filter(
      (todo) => todo.completed,
    ).length;
  }, [todos]);
  
  return (
    <div>
      {/* ... */}
      <div>
        Completed Todos: {completedCount}
      </div>
    </div>
  );
};

export default TodoList;
```

- `TodoList`에서 `memo` 및 `userMemo` 함수 제거하기

```javascript
import React, {
  useState,
  useEffect,
} from "react";

// memo()가 필요하지 않다.
const TodoItem = ({ todo }) => {
  return <li>{todo}</li>;
};

const TodoList = () => {
  const [todos, setTodos] = useState([]);

  useEffect(() => {
    // ...
  }, []);

  // useMemo()가 필요하지 않다.
  const completedCount = todos.filter(
    (todo) => todo.completed,
  ).length;

  return (
    <div>
      <ul>
        {todos.map((todo) => (
          <TodoItem
            key={todo.id}
            todo={todo.todo}
          />
        ))}
      </ul>
      <div>
        Completed Todos: {completedCount}
      </div>
    </div>
  );
};

export default TodoList;
```

- `TodoList`에서 가상의 값비싼 함수 사용하기

```javascript
import React from "react";

// 리액터 컴파일러는 함수 자체를 메모화하지는 않는다.
const getPriorityTodos = (todos) => {
  // ...
};

const TodoList = () => {
  // ...

  // 리액터 컴파일러는 함수 호출을 메모화한다.
  const priorityTodos = getPriorityTodos(items);
    return <div>{/* ... */}</div>;
};

export default TodoList;
```

- 속성 존재 여부를 조건으로 확인하기

```javascript
/*
  nullableProperty가 null 혹은 undefined가 아니면
  'foo' 속성에 접근한다.
*/
if (object.nullableProperty) {
  return object.nullableProperty.foo
}
```

- 옵셔널 체이닝을 사용해 속성에 안전하게 접근하기

```javascript
/*
  옵셔널 체이닝을 사용해 'foo' 속성에 안전하게 접근한다.
*/
return object.nullableProperty?.foo
```

## 18.4. 리액트 서버 컴포넌트

- BlogPost  서버 컴포넌트

```javascript
import db from "./database";

// 리액트 서버 컴포넌트
async function BlogPost({ postId }) {
  // 데이터베이스에서 블로그 포스트 데이터를 로딩한다.
  const post = await db.posts.get(postId);

  return (
    <div>
      <h2>{post.title}</h2>
      <p>{post.summary}</p>
      <p>Written by {post.author}</p>
      <p>{post.content}</p>
    </div>
  );
}
```

- `Comment` 클라이언트 컴포넌트 리스트를 렌더링하는 `BlogPost`

```javascript
import db from "./database";

// 리액트 서버 컴포넌트
async function BlogPost({ postId }) {
  const post = await db.posts.get(postId);

  // 데이터베이스에서 포스트에 대한 댓글을 로딩한다.
  const comments =
    await db.comments.getByPostId(postId);

  return (
    <div>
      <h2>{post.title}</h2>
      <p>{post.summary}</p>
      <p>Written by {post.author}</p>
      <p>{post.content}</p>

      // Render list of Comment Client components
      <ul>
        {comments.map((comment) => (
          <li key={comment.id}>
            <Comment {...comment} />
          </li>
        ))}
      </ul>
    </div>
  );
}
```

- `Comment` 클라이언트 컴포넌트

```javascript
"use client";
import { useState } from 'react'

// 리액트 클라이언트 컴포넌트
export function Comment({ id, text }) {
  const [likes, setLikes] = useState(0);

  function handleLike() {
    setLikes(likes + 1);
  }

  return (
    <div>
      <p>{text}</p>
      <button onClick={handleLike}>
        Like ({likes})
      </button>
    </div>
  );
}
```

- `comments`가 아닌 `post` 콘텐츠를 기다리기

```javascript
import { Suspense } from "react";
import { Comments } from "./comment";
import db from "./database";

// 비동기 컴포넌트
async function BlogPost({ postId }) {
  // 여기에서 기다린다.
  const post = await db.posts.get(postId);

  // 여기에서는 기다리지 않는다.
  const comments =
    db.comments.getByPostId(postId);

  return (
    <div>
      <h2>{post.title}</h2>
      <p>{post.summary}</p>
      <p>Written by {post.author}</p>
      <p>{post.content}</p>
      <Suspense
        fallback={<p>Loading comments...</p>}
      >
        <Comments commentsPromise={comments} />
      </Suspense>
    </div>
  );
}
```

- `use()`를 사용해 프로미스 구독하기

```javascript
"use client";
import { use } from "react";
import { Comment } from "./Comment";

// 리액트 클라이언트 컴포넌트
export function Comments({ commentsPromise }) {
  const comments = use(commentsPromise);
  
  return (
    <ul>
      {comments.map((comment) => (
        <li key={comment.id}>
          <Comment {...comment} />
        </li>
      ))}
    </ul>
  );
}
```

- 서버 컴포넌트 안에서 생성된 서버 액션

```javascript
import { Comments } from "./Comments";
import db from "./database";

async function BlogPost({ postId }) {
  // ...

  // 서버 액션
  async function upvoteAction(commentId) {
    "use server";
    await db.comments.incrementVotes(
      commentId,
      1,
    );
  }

  return (
    <div>
      {/* ... */}

      {/* 서버 액션은 props로서 아래로 전달된다. */}
      <Comments
        commentsPromise={comments}
        upvoteAction={upvoteAction}
      />
    </div>
  );
}
```

- `Comments` 클라이언트 컴포넌트에서 사용된 서버 액션

```javascript
"use client";
import { use } from "react";

export function Comments({
  commentsPromise,
  upvoteAction,
}) {
  const comments = use(commentsPromise);
    
  return (
    <ul>
      {comments.map((comment) => (
        <li key={comment.id}>
          {comment.text} (Votes: {comment.votes})
          <button onClick={upvoteAction}>
            Upvote
          </button>
        </li>
      ))}
    </ul>
  );
}
```

- 별도 파일에 정의된 서버 액션

```javascript
"use server";

import db from "./database";

export async function upvoteAction(commentId) {
  await db.comments.incrementVotes(
    commentId,
    1,
  );
}

export async function downvoteAction(
  commentId,
) {
  await db.comments.incrementVotes(
    commentId,
    -1,
  );
}
```
