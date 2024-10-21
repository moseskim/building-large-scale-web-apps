8 국제화
===

## 8.1. 코드에서 텍스트와 콘텐츠를 분리하라

- 텍스트 문자열 하드 코딩하기

```javascript
const greetingVariable = 'Hello, World!';
```

- 텍스트 문자열을 `en.json` 파일에 저장하기

```json
{
  "greeting": "Hello, World!"
}
```

- 키를 사용해 en.json 파일의 텍스트에 접근하기

```javascript
const greetingVariable = {{ greeting }};
```

- 프랑스어로 번역된 텍스트 문자열을 `fr.json`에 저장하기

```json
{
  "greeting": "Bonjour, le monde !"
}
```

- 스페인어로 번역된 텍스트 문자열을 `es.json` 파일에 저장하기

```json
{
  "greeting": "¡Hola, Mundo!"
}
```

- 독일어로 번역된 텍스트 문자열어 `de.json`에 저장하기

```json
{
  "greeting": "Hallo, Welt!"
} 
```

- 텍스트 문자열을 변수에 저장하기

```javascript
{
  "greeting": "Hello, {{name}}!"
}
```

- 템플릿 문자열 삽입

```javascript
const userName = "Addy";
const greetingMessage = interpolate(
  "{{ greeting }}",
  { name: userName }
);

// greetingMessage === "Hello, Addy!"
```

## 8.2. 서드파티 지역화 라이브러리를 활용하라

- react-intl 라이브러리 설치하기

```shell
yarn add react-intl
```

- `src/locales/en.json`

```json
{
  "greeting": "Hello, User!"
}
```

- `src/locales/fr.json`

```json
{
  "greeting": "Bonjour, le monde !"
}
```

- `src/locales/es.json`

```json
{
  "greeting": "¡Hola, Mundo!"
}
```

- `src/locales/de.json`

```json
{
  "greeting": "Hallo, Welt!"
}
```

- `IntlProvider`로 애플리케이션 감싸기

```javascript
import { IntlProvider } from "react-intl";

import messages_en from "./locales/en.json";
import messages_es from "./locales/es.json";
import messages_fr from "./locales/fr.json";
import messages_de from "./locales/de.json";

const messages = {
  en: messages_en,
  es: messages_es,
  fr: messages_fr,
  de: messages_de,
};

export default function App() {
  const locale = "en"; // 기본 로케일
  
  return (
    <IntlProvider
      locale={locale}
      messages={messages[locale]}
    >
      {/* 애플리케이션의 나머지 부분 */}
    </IntlProvider>
  );
}
```

- `FormattedMessage`를 사용해 텍스트 표시하기

```javascript
// ...
import {
  IntlProvider,
  FormattedMessage,
} from "react-intl";

export default function App() {
  const locale = "en";

  return (
    <IntlProvider
      locale={locale}
      messages={messages[locale]}
    >
      <FormattedMessage id="greeting" />
    </IntlProvider>
  );
}
```

- 컴포넌트 상태에 로케일 저장하기

```javascript
import { useState } from 'react';
// ...

export default function App() {
  const [locale, setLocale] = useState('en');

  // ...
}
```

- 로케일 변경 트리거하기

```javascript
import { useState } from "react";
import {
  FormattedMessage,
  IntlProvider,
} from "react-intl";
// ...

export default function App() {
  const [locale, setLocale] = useState("en");

  return (
    <IntlProvider
      locale={locale}
      messages={messages[locale]}
    >
      <FormattedMessage id="greeting" />
      <div>
        <button onClick={() => setLocale("en")}>
          English
        </button>
        <button onClick={() => setLocale("es")}>
          Español
        </button>
        <button onClick={() => setLocale("fr")}>
          Français
        </button>
        <button onClick={() => setLocale("de")}>
          Deutsch
        </button>
      </div>
    </IntlProvider>
  );
}
```

## 8.3. 동적 로딩

- 동적으로 로케일 임포트하기

```javascript
import React, { useState, useEffect } from "react";
import {
  IntlProvider,
  FormattedMessage
} from "react-intl";

export function App() {
  // 초기 상태의 기본 로케일 설정
  const [locale, setLocale] = useState("en");
  const [messages, setMessages] = useState({});

  useEffect(() => {
    /*
      로케일에 기반해 필요한 번역 파일을 동적으로 임포트한다.
    */
    const loadMessages = async () => {
      switch (locale) {
        case "en":
          await import(
            "./locales/en.json"
          ).then((m) => setMessages(m.default));
          break;
        case "es":
          await import(
            "./locales/es.json"
          ).then((m) => setMessages(m.default));
          break;
        // 기타 로케일 
      }
    };

    loadMessages();
  }, [locale]);

  return (
    <IntlProvider locale={locale} messages={messages}>
      {/* ... */}
    </IntlProvider>
  );
}
```

## 8.4. 여러 언어에서의 복수형 처리하기

- 아이템 수 메시지에 관한 복수 규칙 정의하기

```javascript{
  "itemCountMessage": "{itemCount, plural, =0
  {No items} one {1 item} other {You have
  {itemCount} items}}"
}
```

- 아랍어에서 아이템 수 메시지에 복수 규칙 정의하기

```javascript
{
  "itemCountMessage": "{itemCount, plural, =0
  {ليس لديك أي عناصر} one {لديك عنصر واحد}
  two {لديك عنصرين} few {{itemCount} عناصر ديك}
  many {لديك العديد من العناصر} other
  {{itemCount} عنصرًا}}"
}
```

- 아이템 수 메시지 렌더링하기

```javascript
import { FormattedMessage } from 'react-intl';

function PluralComponent({ itemCount }) {
  return (
    <FormattedMessage
      id="itemCount"
      values={{ itemCount }}
    />
  );
}
```

## 8.5. 날짜, 시간, 숫자 형식 나타내기

- `Intl` 자바스크립트 객체를 사용해 날짜 형식 지정하기

```javascript
const date = new Date();
const options = {
  year: "numeric",
  month: "long",
  day: "numeric",
};
const formattedDate = new Intl.DateTimeFormat(
  "en-US",
  options,
).format(date);

console.log(formattedDate);
// 출력: June 30, 2023
```

- `Intl` 자바스크립트 객체를 사용해 숫자 형식 지정하기

```javascript
const number = 1004580.89;
const formattedNumberDE = new Intl.NumberFormat(
  "de-DE",
).format(number);
const formattedNumberUS = new Intl.NumberFormat(
  "en-US",
).format(number);

// 출력: 1.004.580,89
console.log(formattedNumberDE);

// 출력: 1,004,580.89
console.log(formattedNumberUS);
```

- `react-intl`의 `FormattedDate` 컴포넌트를 사용해 날짜 형식 지정하기

```javascript
import { FormattedDate } from "react-intl";

// ...

// 컴포넌트 렌더링하기
<FormattedDate
  value={new Date()}
  locale="en-US"
/>
```

- `en-GB` 로케일로 날짜 형식 지정하기

```javascript
import { FormattedDate } from "react-intl";

// ...

// 컴포넌트 렌더링하기
<FormattedDate
  value={new Date()}
  locale="en-GB"
/>  
```

- 긴 날짜 형식으로 렌더링하기

```javascript
import { FormattedDate } from 'react-intl';

// 컴포넌트 렌더링하기
<FormattedDate
  value={new Date()}
  locale="en-US"
  weekday="long"
  year="numeric"
  month="long"
  day="numeric"
/>
```

- `react-intl` `FormattedNumber` 컴포넌트를 사용해 숫자 형식 지정하기

```javascript
import { FormattedNumber } from "react-intl";

// ...

// 독일 로케일로 컴포넌트 렌더링하기
<FormattedNumber
  value={1004580.89}
  style="currency"
  currency="EUR"
  locale="de-DE"
/>

// 미국 로케일로 컴포넌트 렌더링하기
<FormattedNumber
  value={1004580.89}
  style="currency"
  currency="USD"
  locale="en-US"
/>
```

## 8.6. 오른쪽에서 왼쪽으로 쓰는 언어를 고려하라

- HTML의 `dir` 속성

```html
<p dir="ltr">
  This paragraph is in English and correctly
  goes left to right.
</p>

<p dir="rtl">
  هذه الفقرة باللغة العربية ، لذا يجب الانتقال
  من اليمين إلى اليسار.
</p>
```

- 문서 방향에 기반해 텍스트 정렬하기

```css
/*
  dir="ltr"이면 텍스트를 왼쪽 기준으로 정렬하고
  dir="rtl"이면 텍스트를 오른쪽 기준으로 정렬한다.
*/
.text-start {
  text-align: start;
}

/*
  dir="ltr"이면 텍스트를 오른쪽 기준으로 정렬하고
  dir="rtl"이면 텍스트를 왼쪽 기준으로 정렬한다.
*/
.text-end {
  text-align: end;
}
```

- 문서 끝을 기준으로 텍스트 정렬하기

```html
<p dir="ltr" class="text-end">
  This paragraph is in English and is aligned
  at the end.
</p>

<p dir="rtl" class="text-end">
  هذه الفقرة مكتوبة باللغة الإنجليزية وتمت
  محاذاتها في النهاية.
</p>
```

- 대체 폰트

```css
body {
  font-family: 'Noto', 'Monotype SST', '...';
}
```

- 다양한 언어에 관한 대체 폰트

```css
/* 기본 폰트 스택 */
body {
  font-family: 'Noto', 'Monotype SST';
}

/* 아랍어 콘텐츠(RTL)에 관한 폰트 스택 */
body[lang="ar"] {
  font-family: 'Noto Naskh Arabic', 'Tahoma';
}

/* 히브리어 콘텐츠(RTL)에 관한 폰트 스택 */
body[lang="he"] {
  font-family: 'Frank Ruhl Libre', 'Arial Hebrew';
}
```

- 영어로 작성된 텍스트 왼쪽에 위치한 아이콘(HTML)

```html
<div class="icon-text-ltr">
  <img
    src="icon.png"
    alt="Icon"
    class="icon-ltr"
  />
  <p>
    This is some English text with an icon on
    the left.
  </p>
</div>
```

- 영어로 작성된 텍스트 왼쪽에 위치한 아이콘(CSS)

```css
.icon-text-ltr {
  display: flex;
  align-items: center;
}

.icon-ltr {
  width: 20px;
  /* 아이콘은 테스트 왼쪽에 위치한다. */
  margin-right: 10px;
}
```

- `margin-inline-end` CSS 속성을 사용한 아이콘 배치

```css
.icon-text-ltr {
  display: flex;
  align-items: center;
}

.icon-ltr {
  width: 20px;
  /*
    inline 방향에서 아이콘 뒤에 바깥쪽 여백이 적용된다.
  */
  margin-inline-end: 10px;
}
```