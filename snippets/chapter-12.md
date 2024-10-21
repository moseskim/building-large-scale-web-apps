12 테스팅
=====

## 12.1. 단위 테스트

- `Counter` 컴포넌트

```javascript
import React, { useState } from 'react';

const Counter = () => {
  const [count, setCount] = useState(0);
  
  const increment = () => {
    setCount(count + 1);
  };
  
  const decrement = () => {
    setCount(count \- 1);
  };
  
  return (
    <div>
      <button onClick={decrement}>\-</button>
      <span>{count}</span>
      <button onClick={increment}>+</button>
    </div>
  );
};

export default Counter;
```

- `Counter.test.tsx` 파일

```javascript
import React from 'react';

describe('Counter component', () => {
  it('initial count is 0', () => {
    // ...
  });

  it('increases count on "+" button click', () => {
    // ...
  });
  
  it('decreases count on "-" button click', () => {
    // ...
  });
});
```

- `count` 초깃값이 0인지 테스트하기

```javascript
import React from 'react';
import { render } from '@testing-library/react';
import Counter from './Counter'

describe('Counter component', () => {
  it('renders with initial count of 0', () => {
    const { getByText } = render(<Counter />);
    expect(getByText('0')).not.toBeNull();
  });

  // ...
});
```

- `count` 증가 및 감소에 관한 테스팅

```javascript
import React from 'react';
import {
  render,
  fireEvent
} from '@testing-library/react';
import Counter from './Counter'

describe('Counter component', () => {
  it('renders with initial count of 0', () => {
    const { getByText } = render(<Counter />);
    expect(getByText('0')).not.toBeNull();
  });

  it('increases count on "+" button click', () => {
    const { getByText } = render(<Counter />);
    const incrementButton = getByText('+');

fireEvent.click(incrementButton);
    expect(getByText('1')).not.toBeNull();
  });

  it('decreases count on "-" button click', () => {
    const { getByText } = render(<Counter />);
    const decrementButton = getByText('-');

fireEvent.click(decrementButton);
    expect(getByText('-1')).not.toBeNull();
  });
});
```

- 정리, 동작, 확인 패턴을 Counter 컴포넌트 테스트에 적용하기

```javascript
import React from 'react';
import {
  render,
  fireEvent
} from '@testing-library/react';
import Counter from './Counter'

describe('Counter component', () => {
  it('renders with initial count of 0', () => {
    // 정리
    const { getByText } = render(<Counter />);

    /*
      동작 - 초기 렌더링을 테스트하므로 아무런 동작이 필요하지 않다.
    */

    // 확인
    expect(getByText('0')).not.toBeNull();
  });

  it('increases count on "+" button click', () => {
    // 정리
    const { getByText } = render(<Counter />);
    const incrementButton = getByText('+');
    
    // 동작
    fireEvent.click(incrementButton);

    // 확인
    expect(getByText('1')).not.toBeNull();
  });

  it('decreases count on "-" button click', () => {
    // 정리
    const { getByText } = render(<Counter />);
    const decrementButton = getByText('-');
    
    // 동작
    fireEvent.click(decrementButton);
  
    // 확인
    expect(getByText('-1')).not.toBeNull();
  });
});
```

## 12.2. 엔드-투-엔드 테스트

- 메인 `App` 컴포넌트

```javascript
import React from "react";
import {
  BrowserRouter as Router,
  Route,
  Switch,
} from "react-router-dom";
import HomePage from "./HomePage";
import CounterList from "./CounterList";

function App() {
  return (
    <Router>
      <Switch>
        <Route
          exact
          path="/"
          component={HomePage}
        />
        <Route
          path="/counter"
          component={CounterList}
        />
      </Switch>
    </Router>
  );
}

export default App;
```

- `HomePage` 컴포넌트

```javascript
import React from "react";
import { Link } from "react-router-dom";

const HomePage = () => (
  <div>
    <h1>Welcome to the Counter app</h1>
    <Link to="/counter">Go to Counter</Link>
  </div>
);

export default HomePage;
```

- `CounterList` 컴포넌트

```javascript
import React, {
  useState,
  useEffect,
} from "react";
import Counter from "./Counter";

const CounterList = () => {
  const [items, setItems] = useState([]);

  useEffect(() => {
    fetch("/api/items")
      .then((response) => response.json())
      .then((data) => setItems(data));
  }, []);

  return (
    <div>
      <h1>Counter List</h1>
      {items.map((item) => (
        <Counter key={item.id} item={item} />
      ))}
    </div>
  );
};

export default CounterList;
```

- `Counter` 컴포넌트

```javascript
import React, { useState } from "react";

const Counter = ({ item }) => {
  const [count, setCount] = useState(item.count);
  
  const updateCount = (change) => {
    fetch(`/api/items/${item.id}`, {
      method: "PUT",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        count: count + change,
      }),
    })
      .then((response) => response.json())
      .then((updatedItem) => {
        setCount(updatedItem.count);
      });
  };
    
  return (
    <div>
      <h3>{item.name}</h3>
      <div>
        Count:{" "}
        <span className="count">{count}</span>
      </div>
      <button
        className="increment"
        onClick={() => updateCount(1)}
      >
        +
      </button>
      <button
        className="decrement"
        onClick={() => updateCount(-1)}
      >
        -
      </button>
    </div>
  );
};
```

- “`/`” 라우트 테스트하기

```javascript
describe('/', () => {
  beforeEach(() => {
    cy.visit('/');
  });
  
  it('redirects to /counter on link click', () => {
    cy.get('a').click();
    cy.url().should('include', '/counter');
  });
});
```

- “`/counter`” 라우트에 대한 테스트 설정하기

```javascript
describe('/counter', () => {
  beforeEach(() => {
    cy.visit('/counter');
  });

  // ...
});
```

- “`/counter`”에 대한 GET 요청을 스텁으로 만들기

```javascript
describe('/counter', () => {
  beforeEach(() => {
    cy.intercept('GET', '/api/items', [
      { id: 1, name: 'Item 1', count: 0 },
      { id: 2, name: 'Item 2', count: 0 },
    ]);
    cy.visit('/counter');
  });

  // ...
});
```

- “`/counter`”에 대한 GET 요청을 스텁으로 만들기

```javascript
describe('/counter', () => {
  beforeEach(() => {
    cy.intercept('GET', '/api/items', [
      { id: 1, name: 'Item 1', count: 0 },
      { id: 2, name: 'Item 2', count: 0 },
    ]);
    cy.visit('/counter');
  });

  // ...
});
```

- “`/counter`” 라우트에서의 데이터 가져오기 및 표시 테스트하기

```javascript
describe('/counter', () => {
  beforeEach(() => {
    cy.intercept('GET', '/api/items', [
      { id: 1, name: 'Item 1', count: 0 },
      { id: 2, name: 'Item 2', count: 0 },
    ]);
    cy.visit('/counter');
  });

  it('displays counters fetched from API', () => {
    cy.get('.count').should(($counts) => {
      expect($counts).to.have.length(2);
      expect($counts.eq(0)).to.contain.text('0');
      expect($counts.eq(1)).to.contain.text('0');
    });
  });

  // ...
});
```

- “`/counter`” 라우트에서 증가 기능 테스트하기

```javascript
describe("/counter", () => {
  beforeEach(() => {
    cy.intercept('GET', '/api/items', [
      { id: 1, name: 'Item 1', count: 0 },
      { id: 2, name: 'Item 2', count: 0 },
    ]);
    cy.visit("/counter");
  });

  it("displays counters fetched from API", () => {
    cy.get(".count").should(($counts) => {
      expect($counts).to.have.length(2);
      expect($counts.eq(0)).to.contain.text("0");
      expect($counts.eq(1)).to.contain.text("0");
    });
  });

  it("increases count on increment click", () => {
    const updatedItem = {
      id: 1,
      name: "Item 1",
      count: 2,
    };

    cy.get(".increment").first().click().click();

    cy.get(".count")
      .first()
      .should("contain.text", updatedItem.count);
  });

  // ...
});
```

- “`/counter`” 라우트에서 감소 기능 테스트하기

```javascript
describe("/counter", () => {
  beforeEach(() => {
    cy.visit("/counter");
  });

  it("displays counters fetched from API", () => {
    cy.get(".count").should(($counts) => {
      expect($counts).to.have.length(2);
      expect($counts.eq(0)).to.contain.text("0");
      expect($counts.eq(1)).to.contain.text("0");
    });
  });

  it("increases count on increment click", () => {
    const updatedItem = {
      id: 1,
      name: "Item 1",
      count: 2,
    };

    cy.get(".increment").first().click().click();

    cy.get(".count")
      .first()
      .should("contain.text", updatedItem.count);
  });

  it('decreases count on decrement click', () => {
    const updatedItem = {
      id: 2,
      name: 'Item 2',
      count: -2,
    };

    cy.get('.decrement').first().click().click();

    cy.get('.count')
      .first()
      .should("contain.text", updatedItem.count);
  });
});
```

## 12.3. 통합 테스트

- `Counter` 컴포넌트와 API 서비스에 대한 통합 테스팅

```javascript
import React from "react";
import {
  render,
  fireEvent,
  waitFor,
} from "@testing-library/react";
import { Counter } from "./Counter";

// 목 API 서비스
jest.mock("./apiService", () => ({
  updateCount: jest.fn(),
}));

describe("Counter", () => {
  test("UI updates on item count++", async () => {
    const mockData = {
      id: 1,
      name: "Item 1",
      count: 2,
    };
    const { queryByText } = render(
      <Counter
        item={{
          id: 1,
          name: "Item 1",
          count: 0,
        }}
      />,
    );

    // 증가 버튼을 클릭한다.
    fireEvent.click(queryByText("+"));

    // API 응답을 기다린다.
    await waitFor(() =>
      expect(
        require("./apiService").updateCount,
      ).toHaveBeenCalledWith(1, mockData.count),
    );

    // 새로운 데이터를 반영한 UI 업데이트를 확인한다.
    expect(
      queryByText("Count: 2"),
    ).not.toBeNull();
  });
});
```

### 12.4. 스냅숏 테스트

- 간단한 `Link` 컴포넌트

```javascript
export default function Link({
  page,
  children,
}) {
  return <a href={page}>{children}</a>;
}
```

- `Link` 컴포넌트에 대한 스냅숏 테스트하기

```javascript
import renderer from "react-test-renderer";
import Link from "../Link";

it("renders correctly", () => {
  const tree = renderer
    .create(
      <Link page="http://www.facebook.com">
        Facebook
      </Link>,
    )
    .toJSON();
  expect(tree).toMatchSnapshot();
});
```

- `Link` 컴포넌트의 초기 스냅숏

```javascript
exports[`renders correctly 1`] = `
<a
  href="http://www.facebook.com"
>
  Facebook
</a>
`;
```

- 다른 링크를 렌더링하도록 테스트를 업데이트하기

```javascript
import renderer from 'react-test-renderer';
import Link from '../Link';

it('renders correctly', () => {
  const tree = renderer
    .create(
      <Link page="http://www.instagram.com">
        Instagram
      </Link>
    )
    .toJSON();
  expect(tree).toMatchSnapshot();
});
```

- Jest 스냅숏 업데이트하기

```shell
jest --updateSnapshot
```

- 업데이트된 `Link` 컴포넌트의 스냅숏

```javascript
exports[`renders correctly 1`] = `
<a
  href="http://www.instagram.com"
>
  Instagram
</a>
`;
```
