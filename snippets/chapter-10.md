10 개인화와 A/B 테스팅
=====

## 10.1. 개인화

- 사용자 컨텍스트와 공급자 설정하기

```javascript
import React from "react";

// UserPreferences 컨텍스트를 만든다.
const UserContext = React.createContext({});

export const UserProvider = ({ children }) => {
  /*
    외부 서비스/API로부터 사용자 선호도 정보를 가져온다.
  */
  const userPreferences = getUserPreferences();
  
  return (
    <UserContext.Provider
      value={{ userPreferences }}
    >
      {children}
    </UserContext.Provider>
  );
};
```

- `UserProvider`를 사용해서 자식 컴포넌트 감싸기

```javascript
import { UserProvider } from './UserContext';

function App() {
  return (
    <UserProvider>
      {/* 다른 컴포넌트들 */}
    </UserProvider>
  );
}

export default App;
```

- 요소의 배경 색상 개인화하기

```javascript
function ChildComponent() {
  const { userPreferences } =
    useContext(UserContext);
    
  // 사용자 데이터로부터 색상 선호도를 추출한다.
  const { backgroundColorPreference } =
    userPreferences;

  return (
    <div
      style={{
        backgroundColor:
          backgroundColorPreference,
      }}
    >
      Personalized content based on user
      preference goes here.
    </div>
  );
}
```

## 10.2. A/B 테스팅

- 리액트 애플리케이션에서 Statsig 초기화하기

```javascript
import { StatsigProvider } from "statsig-react";

function App() {
  return (
    <StatsigProvider
      sdkKey="<STATSIG_CLIENT_SDK_KEY>"
      waitForInitialization={true}
    >
      <div className="App">
        {/* Rest of App ... */}
      </div>
    </StatsigProvider>
  );
}

export default App
```

- 실험 구성 상세 정보 얻기

```javascript
import { useExperiment } from "statsig-react";

function ButtonComponent() {
  // 실험 구성 정보에 접근한다.
  const { config } = useExperiment(
    "button_color",
  );

  // 실험 매개변수 값에 접근한다.
  const showGreenButton = config.get(
    "enable_feature",
  );

  return (
    // ...
  )
}

export default ButtonComponent
```

- 실험 그룹에 기반해 다른 버튼 색상 렌더링하기

```javascript
import { useExperiment } from "statsig-react";

function ButtonComponent() {
  // 실험 환경에 접근한다.
  const { config } = useExperiment(
    "button_color",
  );

  // 실험 매개변수 값에 접근한다.
  const showGreenButton = config.get(
    "enable_feature",
  );

  /*
    실험 그룹에 따라 버튼 색상을 결정한다.
  */
  const buttonColor = showGreenButton
    ? "green"
    : "blue";

  return (
    <button
      style={{ backgroundColor: buttonColor }}
    >
      Click Me
    </button>
  );
}

export default ButtonComponent;
```

- 버튼 클릭 기록하기

```javascript
import {
  Statsig,
  useExperiment,
} from "statsig-react";

function ButtonComponent() {
  const { config } = useExperiment(
    "button_color",
  );

  const showGreenButton = config.get(
    "enable_feature",
  );

  const buttonColor = showGreenButton
    ? "green"
    : "blue";

  const onButtonClick = () => {
    // 버튼 클릭을 기록한다.
    Statsig.logEvent(
      "button_click",
      buttonColor,
    );
  };

  return (
    <button
      style={{ backgroundColor: buttonColor }}
      onClick={onButtonClick}
    >
      Click Me
    </button>
  );
}

export default ButtonComponent;
```

## 10.3. 기능 플래그

- 기능 플래그 활성화 시 녹색 버튼 표시하기

```javascript
import { useGate } from "statsig-react";

function ButtonComponent() {
  // 기능 플래그를 평가한다.
  const { value: isFeatureEnabled } = useGate(
    "new_button_color",
  );

  // 사용할 버튼 색상을 결정한다.
  const buttonColor = isFeatureEnabled
    ? "green"
    : "blue";

  return (
    <button
      style={{ backgroundColor: buttonColor }}
    >
      Click Me
    </button>
  );
}

export default ButtonComponent;
```

