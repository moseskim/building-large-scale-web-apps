5 디자인 시스템
===

## 5.1. 코딩 스타일 가이드

- BEM

```css
.block {}
.block__element {}
.block--modifier {}
```

- BEM 방법론을 사용해 단순한 카드들의 이름을 짓는 클래스

```html
<div class="card card--featured">
  <h2 class="card__title">Featured Product</h2>
  <p class="card__description">
    Lorem ipsum dolor sit amet
  </p>
  <a href="#" class="card__link">Learn More</a>
</div>
```

- Tailwind의 유틸리티 클래스를 사용한 스타일링

```
<div class="p-3 bg-white shadow rounded-lg">
  <h3 class="text-xs border-b">font-mono</h3>
  <p class="font-mono">
    The quick brown fox jumps over the lazy dog.
  </p>
</div>
```

## 5.2. 디자인 토큰

- 색상용 디자인 토큰

```css
$color-primary: #0088cc;
$color-secondary: #3d3d3d;
$color-success: #47b348;
$color-warning: #ffae42;
$color-error: #dc3545;
```

- 폰트 크기 및 간격용 디자인 토큰

```css
/* 폰트 크기 */
$font-size-xs: 12px;
$font-size-sm: 14px;
$font-size-md: 16px;
$font-size-lg: 20px;
$font-size-xl: 24px;

/* 간격 */
$spacing-xs: 4px;
$spacing-sm: 8px;
$spacing-md: 16px;
$spacing-lg: 32px;
$spacing-xl: 64px;
```

- SASS 변수를 사용해 디자인 토큰 정의하기

```css
/* 색상 */
$color-primary: #3498db;

/* 간격 */
$spacing-large: 20px;

/* 폰트 */
$font-family-default: "Arial, sans-serif";
```

- 사용자 지정 CSS 속성을 사용해 디자인 토큰 정의하기

```css
:root {
  /* 색상 */
  --color-primary: #3498db;

  /* 간격 */
  --spacing-large: 20px;

  /* 폰트 */
  --font-family-default: "Arial, sans-serif";
}
```

- JSON을 사용해 디자인 토큰 정의하기

```json
{
  "color": {
    "primary": "#3498db"
  },
  "spacing": {
    "large": "20px"
  },
  "fontFamily": {
    "default": "Arial, sans-serif"
  }
}
```

- YAML을 사용해 디자인 토큰 정의하기

```yaml
color:
  primary: '#3498db'
spacing:
  large: '20px'
fontFamily:
  default: 'Arial, sans-serif'
```

- SASS 기반 스타일시트에 디자인 토큰 임포트하기

```css
// 디자인 토큰 임포트하기
@import 'path-to-your-design-tokens-file.scss';

.button {
  background-color: $color-primary;
  padding: $spacing-large;
  font-family: $font-family-default;
}
```

- CSS 변수

```css
:root {
  --color-primary: #3498db;
  --spacing-large: 20px;
  --font-family-default: "Arial, sans-serif";
}
```

- 사용자 지정 CSS 속성값 삽입하기

```css
.button {
  background-color: var(--color-primary);
  padding: var(--spacing-large);
  font-family: var(--font-family-default);
}
```

- CSS 변수를 사용해 디자인 토큰에 접근하기

```css
// Component.css
.button {
  background-color: var(--color-primary);
  padding: var(--spacing-large);
  font-family: var(--font-family-default);
}
```

```javascript
// Component.jsx
import './Component.css';

function Button() {
  return (
    <button className="button">Click Me</button>
  );
}
```

- JS에서의 디자인 토큰 값 임포트하기

```javascript
import styled from 'styled-components';
import { tokens } from './designTokens.js';

const Button = styled.button`
  background-color: ${tokens.colorPrimary};
  padding: ${tokens.spacingLarge};
  font-family: ${tokens.fontFamilyDefault};
`;

function App() {
  return <Button>Click Me</Button>;
}
```

## 5.3. 컴포넌트 라이브러리

- 컴포넌트 라이브러리 안의 `Button` 컴포넌트

```javascript
import React from "react";
import "./Button.css";

function Button(props) {
  const { children, primary, ...rest } = props;
  return (
    <button
      className={`button ${
        primary ? "button--primary" : ""
      }`}
      {...rest}
    >
      {children}
    </button>
  );
}

export default Button;
```

- `Button` 컴포넌트의 다른 변형으로 렌더링하기

```javascript
import React from "react";
import Button from "@design-system/Button";

function App() {
  return (
    <div>
      <Button primary>
        Click me. I am the primary button!
      </Button>
      <Button>But click me too.</Button>
    </div>  
  );

}

export default App;
```

## 5.4. 접근성

- 이미지를 설명하는 alt 속성 사용하기

```html
<img
  src="cat.jpg"
  alt="A fluffy white cat with green eyes"
/>
```

- 시맨틱/논-시맨틱 접근법을 사용한 헤더 구현

```html
<!-- 논-시맨틱 헤딩-->
<div class="header">
  <div class="logo">Company Logo</div>
  <div class="title">Page Title</div>
</div>

<!-- 시맨틱 헤딩 -->
<header>
  <img src="logo.png" alt="Company Logo">
  <h1>Page Title</h1>
</header>
```
