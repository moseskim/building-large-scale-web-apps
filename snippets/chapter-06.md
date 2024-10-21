6 데이터 가져오기
=====

## 6.1. 브라우저 API와 간단한 HTTP 클라이언트

- Fetch API를 사용해 GET 요청을 만들기

```javascript
import { useEffect } from "react";

const App = () => {
  // ...

useEffect(() => {
    async function fetchData() {
      try {
        const response = await fetch(
          "https://api.example.com/data",
        );
        if (!response.ok) {
          throw new Error(
            "Network response was not ok",
          );
        }
        const data = await response.json();
        console.log(data);
      } catch (error) {
        console.error(
          "There was a problem:",
          error.message,
        );
      }
    }
    
    fetchData();
  }, []);
  // ...
};
```

- Axios HTTP 라이브러리를 사용해 GET 요청 만들기

```javascript
import { useEffect } from "react";
import axios from "axios";

const App = () => {
  // ...

  useEffect(() => {
    async function fetchData() {
      try {
        const response = await axios.get(
          "https://api.example.com/data",
        );
        console.log(response.data);
      } catch (error) {
        console.error(
          "There was a problem:",
          error.message,
        );
      }
    }

    fetchData();
  }, []);
  // ...
};
```

## 6.2. 보다 세련된 데이터 가져오기 라이브러리

- 리액트 쿼리 초기화하기

```javascript
import { createRoot } from "react-dom/client";
import {
  QueryClient,
  QueryClientProvider
} from "@tanstack/react-query";
import { App } from './App';

const rootElement = document.getElementById("root");
const root = createRoot(rootElement);

// 클라이언트를 만든다.
const queryClient = new QueryClient();

root.render(
  // 클라이언트를 애플리케이션에 제공한다.
  <QueryClientProvider client={queryClient}>
    <App />
  </QueryClientProvider>
);
```

- `useQuery` 훅을 사용해 데이터 가져오기

```javascript
import { useQuery } from "@tanstack/react-query";
import axios from "axios";

const fetchTodoList = async () => {
  const response = await axios.get(
    "https://jsonplaceholder.typicode.com/todos",
  );
  return response.data;
};

export function App() {
  const { data, isLoading, isError } = useQuery({
    queryKey: ["todos"],
    queryFn: fetchTodoList,
  });

  // ... 데이터를 표시/조작하기 위한 추가 코드
}
```

- useQuery 반환값에 따라 UI 렌더링하기

```javascript
import { useQuery } from "@tanstack/react-query";
import axios from "axios";

const fetchTodoList = async () => {
  const response = await axios.get(
    "https://jsonplaceholder.typicode.com/todos"
  );
  return response.data;
};

export function App() {
  const { data, isLoading, isError } = useQuery({
    queryKey: ["todos"],
    queryFn: fetchTodoList,
  });

  if (isLoading) {
    return <p>Request is loading!</p>;
  }
  
  if (isError) {
    return <p>Request has failed :(!</p>;
  }

  return (
    <div>
      <h1>Todos List</h1>
      <ul>
        {data.map(todo => (
          <li key={todo.id}>{todo.title}</li>
        ))}
      </ul>
    </div>
  );
}
```

- useQuery 훅을 사용해 데이터 가져오기

```javascript
import { useQuery } from '@tanstack/react-query';
import axios from 'axios';

const fetchTodoList = async () => {
  const response = await axios.get(
    'https://jsonplaceholder.typicode.com/todos'
  );
  return response.data;
}

export function App() {
  const { data, isLoading, isError } = useQuery({
    queryKey: ['todos'],
    queryFn: fetchTodoList
  });
  // ... 데이터를 표시/조작하기 위한 추가 코드
}
```

- staleTime 옵션 설정하기

```javascript
import { useQuery } from '@tanstack/react-query';
import axios from 'axios';

const fetchTodoList = async () => {
  const response = await axios.get(
    'https://jsonplaceholder.typicode.com/todos'
  );
  return response.data;
}

export function App() {
  const { data, isLoading, isError } = useQuery({
    queryKey: ['todos'],
    queryFn: fetchTodoList,
    staleTime: 1000 * 60, // 1분
  });

  // ... 데이터를 표시/조작하기 위한 추가 코드
}
```

## 6.3. 캐시 업데이트하기

- 새로운 todo 아이템을 추가하는 변경 트리거하기

```javascript
import {
  useMutation
} from "@tanstack/react-query";
import axios from "axios";

const createTodo = async (newTodo) => {
  const response = await axios.post(
    "https://jsonplaceholder.typicode.com/todos",
    newTodo,
  );
  return response.data;
};

export function App() {
  const { mutate, isLoading, isError } = 
    useMutation({
      mutationFn: createTodo,
    }); 

  const triggerAddTodo = () => {
    mutate({
      title: "Groceries",
      description:
        "Complete the weekly grocery run",
    });
  };

  return (
    <div>
      <button onClick={triggerAddTodo}>
        Add Todo
      </button>
      {isLoading && <p>Adding todo...</p>}
      {isError && (
      <p>Uh oh, something went wrong!</p>
      )}
    </div>
  );
}
```

- 변경이 성공한 뒤 캐시를 업데이트하기

```javascript
// ...
import { queryClient } from ".";
// ...

function App() {
  const { mutate, isLoading, isError } =
  useMutation({
    mutationFn: createTodo,
    // 변경 성공 시
    onSuccess: (data) => {
    // 캐시에서 현재 todos를 가져온다.
    const currentTodos =
      queryClient.getQueryData(["todos"]);

    // 새로운 todo로 todos 캐시를 업데이트한다.
    queryClient.setQueryData(
      ["todos"],
      [...currentTodos, data],
    );
    },
  });
  // ...
}
```

- 트리거 했을 때 쿼리를 미리 가져오는 함수 작성하기

```javascript
import { useQuery } from "@tanstack/react-query";
import axios from "axios";
import { queryClient } from ".";

const fetchTodoList = async () => {
  const response = await axios.get(
    "https://jsonplaceholder.typicode.com/todos",
  );
  return response.data;
};

const prefetchTodos = async (queryClient) => {
  /*
    이 쿼리의 결과는 일반적인 쿼리와 동일하게 캐시된다.
  */
  await queryClient.prefetchQuery({
    queryKey: ["todos"],
    queryFn: fetchTodoList,
  });
};

// ...
```

- `placeholderData` 쿼리 옵션 사용하기

```javascript
import { useQuery } from "@tanstack/react-query";
import axios from "axios";

const fetchTodoList = async () => {
  const response = await axios.get(
    "https://jsonplaceholder.typicode.com/todos",
  );
  return response.data;
};

function App() {
  const {
    data = [],
    isLoading,
    isError,
  } = useQuery({
    queryKey: ["todos"],
    queryFn: fetchTodoList,
    placeholderData: [
      {
        id: "placeholder1",
        title: "Fetching...",
        completed: false,
      },
      {
        id: "placeholder2",
        title: "Fetching...",
        completed: false,
      },
    ],
  });

  // ...
}
```

- 쿼리 재시도 비활성화하기

```javascript
const { data } = useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodoList,
  retry: false // 모든 재시도를 비활성화한다.
});
```

- 쿼리를 무한 재시도하기

```javascript
const { data } = useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodoList,
  // 쿼리가 성공할 때까지 재시도한다.
  retry: true
});
```

- 10번 재시도하도록 설정하기

```javascript
const { data } = useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodoList,
  // 10번 재시도한 뒤 실패하면 에러를 표시한다.
  retry: 10
});
```

- Devtools 컴포넌트 렌더링하기

```javascript
import {
  ReactQueryDevtools
} from '@tanstack/react-query-devtools';
// ...

// ...

root.render(
  <QueryClientProvider client={queryClient}>
    <App />
    {/* Devtools를 root에 위치시킨다. */}
    <ReactQueryDevtools initialIsOpen={false} />
  </QueryClientProvider>
);
```

## 6.4. 효율적인 데이터 가져오기를 위한 팁

- REST를 사용해 todos 리스트 얻기(GET)

```shell
fetch('/todos')
```

- REST 엔드포인트로부터의 모의 응답

```json
[
  {
    "id": 1,
    "title": "Buy groceries",
    "description": "Milk, Bread, and Eggs",
    "creationDate": "2024-01-01",
    "dueDate": "2024-01-11",
    "completionStatus": false
  },
  {
    "id": 2,
    "title": "Schedule dentist appointment",
    "description": "Remember to call Dr. Smith",
    "creationDate": "2024-01-03",
    "dueDate": "2024-01-03",
    "completionStatus": false
  },
  // ... 더 많은 todo들
]
```

- GraphQL을 사용한 todo의 쿼리 리스트

```
{
  todos {
    title
    completionStatus
  }
}
```

- GraphQL 엔드포인트로부터의 모의 응답

```json
{
  "data": {
    "todos": [
      {
        "title": "Buy groceries",
        "completionStatus": false
      },
      {
        "title": "Schedule dentist appointment",
        "completionStatus": false
      },
      // ... 더 많은 todo들
    ]
  }
}
```
