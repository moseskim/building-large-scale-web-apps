16 라우팅
=====

## 16.1. 사용자에게 라우팅이 중요한 이유는 무엇인가?

## 16.2 리액트의 라우팅 설루션

- JSX를 사용해 라우트 구성하기

```jsx
import {
  createBrowserRouter,
  RouterProvider,
  Route,
  createRoutesFromElements,
} from "react-router-dom";

const router = createBrowserRouter(
  createRoutesFromElements(
    <Route>
      <Route path="/" element={<Home />} />
      <Route
        path="/about"
        element={<About />}
      />
      <Route
        path="/contact"
        element={<Contact />}
      />
    </Route>,
  ),
);

function App() {
  return <RouterProvider router={router} />;
}
```

- `Link` 컴포넌트를 사용해 내비게이션 활성화하기

```javascript
import { Link } from 'react-router-dom';

function Navigation() {
  return (
    <nav>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/about">About</Link>
        </li>
        <li>
          <Link to="/contact">Contact</Link>
        </li>
      </ul>
    </nav>
  );
}
```

- 라우트 매개변수와 `loader` prop

```javascript
import {
  useLoaderData,
  useParams,
} from "react-router-dom";

function UserProfile() {
  const { userId } = useParams();
  const userData = useLoaderData();

  return (
    <div>
      <h1>User Profile</h1>
      <p>User ID: {userId}</p>
      <p>User Name: {userData.name}</p>
    </div>
  );
}

const router = createBrowserRouter(
  createRoutesFromElements(
    <Route
      path="/users/:userId"
      element={<UserProfile />}
      loader={async ({ params }) => {
        return fetch(
          `/api/users/${params.userId}`,
        );
      }}
    />,
  ),
);
```

- `Outlet` 컴포넌트를 사용한 중첩된 라우트

```javascript
const router = createBrowserRouter(
  createRoutesFromElements(
    <Route
      path="/dashboard"
      element={<Dashboard />}
    >
      <Route index element={<Overview />} />
      <Route
        path="profile"
        element={<Profile />}
      />
      <Route
        path="settings"
        element={<Settings />}
      />
    </Route>,
  ),
);

function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <nav>
        <Link to="/dashboard">Overview</Link>
        <Link to="/dashboard/profile">
          Profile
        </Link>
        <Link to="/dashboard/settings">
          Settings
        </Link>
      </nav>
      {/*
        이 컴포넌트는 활성화된 자식 라우트 컴포넌트를 렌더링한다.
      */}
      <Outlet />
    </div>
  );
}
```

- `lazy` prop을 사용해서 라우트 정의를 지연 실행하기

```javascript
import {
  createBrowserRouter,
  RouterProvider,
  createRoutesFromElements,
  Route,
} from "react-router-dom";

/*
  동적 로딩을 위해 `lazy` prop을 사용해서 라우트를 정의한다.
*/
let routes = createRoutesFromElements(
  <Route path="/" element={<Layout />}>
    <Route
      path="a"
      lazy={() => import("./a")}
    />
  </Route>,
);

function App() {
  return (
    <RouterProvider
      router={createBrowserRouter(routes)}
    />
  );
}

export default App;
```

- 라우트 속성이 익스포트된 모듈

```javascript
export async function loader({ request }) {
  // ...
}

export function Component() {
  // ...
}

export function ErrorBoundary() {
  // ...
}

// ...
```

- 클라이언트 컴포넌트

```javascript
"use client"

import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button
        onClick={() => setCount(count + 1)}
      >
        Increment
      </button>
    </div>
  );
}
```

- Next.js의 확장된 가져오기 함수를 사용해 서버에서 데이터 가져오기

```javascript
async function getProduct(id) {
  const res = await fetch(
    `https://api.example.com/products/${id}`,
  );

  if (!res.ok) {
    throw new Error("Failed to fetch data");
  }

  const productData = await res.json();

  return productData;
}

async function ProductPage({ params }) {
  const product = await getProduct(params.id);

  return (
    <div>
      <h1>{product.name}</h1>
      <p>{product.description}</p>
    </div>
  );
}

export default ProductPage;
```

