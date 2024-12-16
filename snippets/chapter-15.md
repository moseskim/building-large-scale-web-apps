15 타입스크립트
=====

## 15.1. 타입 안정성

- 숫자 값 `5`로 초기화하기

```javascript
let number = 5;
```

- 같은 변수에 `"5"` 재할당하기

```javascript
let number = 5;

// 다른 코드...

number = "5";
```

- 타입스크립트에서 `number` 데이터 타입 할당하기

```typescript
let number: number = 5;
```

- 타입스크립트에서 변수를 다른 타입에 재할당하기

```typescript
let number: number = 5;

// 다른 코드

/*
  Type 'string' is not assignable to type 'number'.ts(2322)
*/
number = "5";
```

## 15.3. 구성과 린팅

- 리액트 애플리케이션을 위한 `tsconfig.json` 예시

```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "CommonJS",
    "jsx": "react",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*.ts", "src/**/*.tsx"],
  "exclude": ["node_modules"]
}
```

- 개별 타입 체킹 옵션 수동 활성화하기

```json
{
  "compilerOptions": {
    // ...
    "strict": false,
    "noImplicitAny": true,
    "strictNullChecks": true,
    // ...
  },
  // ...
}
```

- ESLint 의존성 설치하기

```shell
yarn add -D eslint \
  @typescript-eslint/parser \
  @typescript-eslint/eslint-plugin
```

- 리액트/타입스크립트 애플리케이션을 위한 `.eslintrc` 파일 예시

```json
{
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaVersion": "latest",
    "sourceType": "module"
  },
  "extends": [
    "plugin:@typescript-eslint/recommended"
  ],
  "rules": {
    "indent": "off",
    "@typescript-eslint/indent": "off"
  }
}
```

## 15.4. 리액트 + 타입스크립트

- 리액트 컴포넌트에서 props 정의하기

```javascript
import React from 'react';

interface Props {
  name: string;
  age: number;
  address?: string;
}

const Person = ({ name, age, address }: Props) => {
  return (
    <div>
      <p>Name: {name}</p>
      <p>Age: {age}</p>
      {address && <p>Address: {address}</p>}
    </div>
  );
};

export default Person;
```

- 정확하지 않은 타입을 사용해 컴포넌트 렌더링하기

```javascript
import React from 'react';
import Person from './Person';

const App = () => {
  return (
    /*
      TypeScript Error:
      Type 'number' is not assignable to type 'string'.
      Type 'string' is not assignable to type 'number'.

      (타입 'number'는 타입 'string'에 할당할 수 없습니다.)
      (타입 'string'은 타입 'number'에 할당할 수 없습니다.)
    */
    <Person name={42} age={"twenty"} />
  );
};

export default App;
```

- `React.ReactNode`를 `children`으로 받는 컴포넌트

```typescript
import React from "react";

interface Props {
  children: React.ReactNode;
}

const Person = ({ children }: Props) => {
  return <div>{children}</div>;
};

export default Person;
```

- 요소의 배열을 갖는 컴포넌트를 `children`으로 렌더링하기

```typescript
import React from "react";
import Person from "./Person";

const App = () => {
  return (
    <Person>
      {[
        <p key="1">Name: James</p>,
        <p key="2">Age: 30</p>,
      ]}
    </Person>
  );
};

export default App;
```

- `React.ReactElement`를 `children`으로 받는 컴포넌트

```typescript
import React from 'react';

interface Props {
  children: React.ReactElement;
}

const Person = ({ children }: Props) => {
  return <div>{children}</div>;
};

export default Person;
```

- 단일 요소를 갖는 컴포넌트를 `children`으로 렌더링하기

```typescript
import React from 'react';
import Person from './Person';

const App = () => {
  return (
    <Person>
      <p>Name: James</p>
    </Person>
  );
};

export default App;
```

```typescript
interface Props {
  children: [
    React.ReactNode,
    number,
    React.ReactElement
  ];
}
```

- 명시적으로 상태의 타입 정의하기

```typescript
const [count, setCount] = useState<number>(0);
```

- 숫자 `count` 속성을 문자열로 업데이트하기

```typescript
import React, { useState } from 'react';

const Counter: React.FC = () => {
  // 명시적으로 count의 타입을 number로 정의한다.
  const [count, setCount] = useState<number>(0);
  
  const updateCount = () => {
    /*
      TypeScript Error:
      Argument of type 'string' is not assignable to parameter of type 'number | ((prevState: number) => number)'.

      ('string' 타입 인수는 'number | ((prevState: number) => number)' 타입 인수에 할당할 수 없습니다.)
    */
   setCount("5");
  };

  return (
    <div>
      <h2>Counter: {count}</h2>
      <button onClick={updateCount}>
        Increment
      </button>
    </div>
  );
};

export default Counter;
```

- 상태 타입과 액션 정의하기

```typescript
import { useReducer } from 'react';

type State = { count: number };
type Action = {
  type: 'increment' | 'decrement'
};

const initialState: State = { count: 0 };

function reducer(
  state: State,
  action: Action
): State {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count \- 1 };
    default:
      throw new Error();
  }
}

const Counter = () => {
  const [state, dispatch] = useReducer(
    reducer,
    initialState
  );

  // ...
};
```

- 컴포넌트로부터 유효하지 않은 타입의 액션 가져오기

```typescript
// ...
const Counter = () => {
  const [state, dispatch] =
    useReducer(reducer, initialState);

  const wrongUpdate = () => {
    /*
      TypeScript Error:
      Argument of type '{ type: 'multiply' }' is not assignable to parameter of type 'Action'. Object literal may only specify known properties, and 'type' does not exist in type 'Action'.

      (타입 '{ type: 'multiply' }'인 인수는 타입 'Action'의 매개변수로 할당할 수 없습니다. 객체 리터럴은 알려진 속성만 명시할 수 있으며, 'type'은 'Action' 타입에 존재하지 않습니다.)
    */
    dispatch({ type: 'multiply' });
  };

  // ...
};
```

- Context API를 사용해 Themable 리액트 컴포넌트 만들기

```typescript
import React, {
  createContext,
  useContext
} from 'react';

type Theme = 'light' | 'dark';
const ThemeContext =
  createContext < Theme > "light";

const ThemedComponent = () => {
  const theme = useContext(ThemeContext);

  const style = {
    background:
      theme === "light" ? "#fff" : "#333",
  };

  return <div style={style}>...</div>;
};

export default ThemedComponent;
```

- 자바스크립트로 작성한 커스텀 `useToggle` 훅

```javascript
import { useState, useCallback } from 'react';

const useToggle = (initialState = false) => {
  const [isOn, setIsOn] = useState(initialState);
  
  const toggle = useCallback(() => {
    setIsOn(prevIsOn => !prevIsOn);
  }, []);
  
  return [isOn, toggle];
};

export default useToggle;
```

- 타입스크립트로 작성한 동일한 `useToggle` 훅

```typescript
import { useState, useCallback } from 'react';

type UseToggle = {
  isOn: boolean;
  toggle: () => void;
};

const useToggle = (
  initialState: boolean
): [boolean, () => void] => {
  const [isOn, setIsOn] = useState(initialState);

  const toggle = useCallback(() => {
    setIsOn(prevIsOn => !prevIsOn);
  }, []);

  return [isOn, toggle] as const;
};

export default useToggle;
```

- 올바르지 않은 `useToggle` 훅의 사용

```javascript
import React from 'react';
import useToggle from './useToggle';

const Component: React.FC = () => {
  /*
    TypeScript Error:
    Argument of type 'string' is not assignable to parameter of type 'boolean'.

    ('string' 타입의 인수는 `boolean 타입 매개변수에 할당할 수 없습니다.)
  */
  const [isOn, setToggle] = useToggle("wrong type");
  return (
    // ...
  );
};

export default Component;
```

- 올바른 `useToggle` 훅의 사용

```typescript
import React from 'react';
import useToggle from './useToggle';

const Component: React.FC = () => {
  /*
    isOn: boolean
    toggle: () => void
  */
  const [isOn, toggle] = useToggle(false);
  
  return (
    // ...
  );
};

export default Component;
```

- 숫자 이늄

```javascript
enum Direction {
  Up = 1,
  Down,
  Left,
  Right
}

// 사용법
// move는 값 1을 갖는다.
const move = Direction.Up;
```

- 문자열 이늄

```typescript
enum Status {
  IDLE = 'IDLE',
  LOADING = 'LOADING',
  SUCCESS = 'SUCCESS',
  ERROR = 'ERROR'
}

// 사용법
// currentStatus는 값 'LOADING'을 갖는다.
const currentStatus = Status.LOADING;
```

- 이늄을 사용해 조건에 따라 UI 렌더링하기

```typescript
import React from 'react';

const status = {
  IDLE: 'IDLE',
  LOADING: 'LOADING',
  SUCCESS: 'SUCCESS',
  ERROR: 'ERROR',
} as const;

type Status = typeof status[keyof typeof status];

interface Props {
  status: Status;
}

const LoadingIndicator: React.FC<Props> = ({
  status,
}) => {
  switch (status) {
    case status.IDLE:
      return null;
    case status.LOADING:
      return <div>Loading...</div>;
    case status.SUCCESS:
      return <div>Data loaded successfully</div>;
    case status.ERROR:
      return <div>Error loading data</div>;
    default:
      return null;
  }
};

export default LoadingIndicator;
```

- `DataTable` 컴포넌트

```typescript
import React from 'react';

type UserData = {
  id: number;
  name: string;
  email: string;
};

interface Props {
  data: Array<UserData>;
  columns: Array<keyof UserData>;
}

const DataTable = ({
  data,
  columns
}: Props) => {
  return (
    <table>
      <thead>
        <tr>
          {columns.map((col, index) => (
            <th key={index}>{col}</th>
          ))}
        </tr>
      </thead>
      <tbody>
        {data.map(
          (item: UserData, index: number) => (
            <tr key={index}>
              {columns.map((col, idx) => (
                <td key={idx}>{item[col]}</td>
              ))}
            </tr>
          ))}
      </tbody>
    </table>
  );
}
```

- 사용자 정보를 사용해 `DataTable` 렌더링하기

```typescript
<DataTable
  data={[
    {
      id: 1,
      name: "John Doe",
      email: "john.doe@example.com",
    },
  ]}
  columns={["id", "name", "email"]}
/>
```

- 유니온 타입을 사용해 다른 형태의 데이터를 받을 수 있도록 `DataTable`을 조정하기

```typescript
import React from 'react';

type UserData = {
  id: number;
  name: string;
  email: string;
};

type ProductData = {
  productId: number;
  productName: string;
  price: number;
};

interface Props {
  data: Array<UserData> | Array<ProductData>;
  columns:
  Array<keyof UserData> |
  Array<keyof ProductData>;
};

const DataTable = ({ data, columns }: Props) => {
  // 구현 ...
};
```

- `DataTable`이 모든 형태의 데이터를 받을 수 있게 조정하기

```typescript
import React from 'react';

interface Props {
  data: any;
  columns: string[];
}

const DataTable = ({ data, columns }: Props) => {
  // 구현...
}
```

- DataTable이 타입 변수를 받게 만들기

```typescript
import React from 'react';

const DataTable = <T extends object>(
  { data, columns }: Props<T>
) => {
  return (
    // ...
  );
};
```

- 타입 변수를 `Props` 제네릭 인터페이스에 전달하기

```typescript
import React from 'react';

interface Props<T> {
  data: Array<T>;
  columns: Array<keyof T>;
}

const DataTable = <T extends object>(
  { data, columns }: Props<T>
) => {
  return (
    // ...
  );
};
```

- 제네릭 `DataTable` 컴포넌트

```typescript
import React from 'react';

interface Props<T> {
  data: Array<T>;
  columns: Array<keyof T>;
}

const DataTable = <T extends object>(
  { data, columns }: Props<T>
) => {
  return (
    <table>
      <thead>
        <tr>
          {columns.map((col, index) => (
            <th key={index}>{col}</th>
          ))}
        </tr>
      </thead>
      <tbody>
        {data.map((item: T, index: number) => (
          <tr key={index}>
            {columns.map((col, idx) => (
              <td key={idx}>
                {item[col as keyof T]}
              </td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
};
```

- 사용자 데이터를 사용해 `DataTable` 렌더링하기

```typescript
import React from "react";
import DataTable from "./DataTable";

const App = () => {
  const userData = [
    { id: 1, name: "John", age: 30 },
    { id: 2, name: "Jane", age: 25 },
  ];
  return <DataTable data={userData} />;
};

export default App;
```

- 제품 데이터를 사용해 `DataTable` 렌더링하기

```typescript
import React from "react";
import DataTable from "./DataTable";

const App = () => {
  const productData = [
    {
      productId: "p1",
      productName: "Laptop",
      price: 1000,
    },
    {
      productId: "p2",
      productName: "Phone",
      price: 500,
    },
  ];

  return <DataTable data={productData} />;
};

export default App;
```

- 명시적으로 데이터 형태 정의하기

```typescript
import React from 'react';
import DataTable from './DataTable';

type UserData = {
  id: number;
  name: string;
  email: string;
};

const App = () => {
  const userData = [
    { id: 1, name: 'John', age: 30 },
    { id: 2, name: 'Jane', age: 25 }
  ];

  return (
    <DataTable<UserData> data={userData} />
  );
};

export default App;
```

## 15.5. 선언 파일들

- 개발 의존성으로 `@types/lodash` 설치하기

```shell
yarn add @types/lodash \-D
```

- 커스텀 선언 파일 예시

```typescript
// index.d.ts
declare module 'hypothetical-library' {
  export function add(
    a: number,
    b: number
  ): number;

  export function subtract(
    a: number,
    b: number
  ): number;
}
```

- 선언 파일을 포함하도록 타입스크립트 구성하기

```typescript
{
  // ...
  "include": [
    "./path/to/your/declarations/*.d.ts",
    // 다른 파일들
  ],
  // ...
}
```

## 15.6. API 결과에 타입 자동 생성

- User 인터페이스

```typescript
interface User {
  id: number;
  name: string;
};
```

- 사용자 가져오기

```typescript
import React, {
  useState,
  useEffect
} from 'react';
import { User } from './types';

const UserDetail: React.FC<{ userId: number }> =
  ({ userId }) => {

  // 사용자의 타입이 "User"가 되도록 설정한다.
  const [user, setUser] =
    useState<User | null>(null);
    useEffect(() => {
      const fetchUser = async () => {
        try {
          const response = await fetch(
            `https://example.com/api/users/${userId}`
          );
          const userData = await response.json();

          /*
            API로부터 얻은 userData를 "User" 타입으로 형변환한다.
          */
          setUser(userData as User);
        }
      };
      
      fetchUser();
    }, [userId]);

    // 사용자 세부 정보를 렌더링한다...
};
```

- GraphQL 스키마 예시

```shell
type User {
  id: ID!
  name: String!
}

type Query {
  getUser(id: ID!): User
}

type Mutation {
  createUser(name: String!): User!
}
```

- GraphQL 스키마로부터 자동 생성된 타입스크립트 타입

```typescript
export type Scalars = {
  ID: { input: string; output: string; }
  String: { input: string; output: string; }
};

export type User = {
  __typename?: 'User';
  id: Scalars['ID'];
  name: Scalars['String'];
};

export type Query = {
  __typename?: 'Query';
  getUser?: Maybe<User>;
};

export type Mutation = {
  __typename?: 'Mutation';
  createUser?: Maybe<User>;
};

export type QueryGetUserArgs = {
  id: Scalars['ID'];
};

export type MutationCreateUserArgs = {
  name: Scalars['String'];
};
```

- YAML을 사용해 작성한 OpenAPI 3.1.0 명세 예시

```yaml
openapi: 3.1.0
info:
  title: User API
  version: 1.0.0
paths:
  /user:
    get:
      operationId: getUser
      parameters:
        - name: id
          in: query
          schema:
            type: string
          responses:
            '200':
              description: Successful response
    post:
      operationId: createUser
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                name:
                  type: string
        responses:
          '201':
            description: User created
components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: string
        name:
          type: string
```

- RESTful API를 사용해 자동 생성한 타입스크립트 타입

```typescript
export interface User {
  id: string;
  name: string;
}

export interface GetUserParameters {
  id: string;
}

export interface CreateUserBody {
  name: string;
}
```

- gRPC 서비스 정의 예시

```grpc
syntax = "proto3";

package user;

service UserService {
  rpc GetUser (GetUserRequest) returns (User);
  rpc CreateUser (CreateUserRequest) returns (User);
}

message User {
  string id = 1;
  string name = 2;
}

message GetUserRequest {
  string id = 1;
}

message CreateUserRequest {
  string name = 1;
}
```

- gRPC 서비스 정의로부터 자동 생성된 타입스크립트 타입

```typescript
// ...

export interface User {
  id: string;
  name: string;
}

export interface GetUserRequest {
  id: string;
}

export interface CreateUserRequest {
  name: string;
}

// ...

/*
  GetUserRequest와 CreateUserRequest에 대한 유사한 암호화(encode)/복호화(decode) 함수들
*/
```
