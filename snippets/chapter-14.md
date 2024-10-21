14 기술적 마이그레이션
=====

## 14.3. 코드모드

- `XYZ`를 상대 경로로 임포트하기

```javascript
import XYZ from '../../../../components/XYZ'
```

- `XYZ`를 절대 경로로 임포트하기

```javascript
import XYZ from 'src/components/XYZ'
```

- Phenax의 `absolute-import-codemod-transform.js` 스크립트

```javascript
const path = require("path");

const SOURCE = "src";
const SOURCE_PATH = path.resolve(SOURCE) + "/";

const getAbsolutePath = (
  importPath,
  filePath,
) => {
  return path
    .resolve(path.dirname(filePath), importPath)
    .replace(SOURCE_PATH, "");
};

const replaceImportPath = (j, node, filePath) =>
  j.importDeclaration(
    node.value.specifiers,
    j.literal(
      getAbsolutePath(
        node.value.source.value,
        filePath,
      ),
    ),
  );

export default function (file, api) {
  const j = api.jscodeshift;
  const filePath = file.path;
  return j(file.source)
    .find(j.ImportDeclaration)
    .replaceWith((node) =>
      replaceImportPath(j, node, filePath),
    )
    .toSource();
}
```

- `jscodeshift` CLI 명령어로 코드모드 스크립트 실행하기

```shell
jscodeshift -t ./absolute-import-codemod-\
  transform.js src/**/*.js
```

- ES6 클래스 컴포넌트 변환 예시

```javascript
import React, { Component } from 'react';

class SimpleComponent extends Component {
  static defaultProps = {
    greeting: 'Hello',
  };

  render() {
    const { greeting, name } = this.props;
    return (
      <div>
        {greeting}, {name}!
      </div>
    );
  }
}

SimpleComponent.propTypes = {
  name: PropTypes.string.isRequired,
};

export default SimpleComponent;
```

- 특정 ES6 클래스를 함수형 컴포넌트로 변환하는 코드모드

```javascript
const { parse } = require("recast");
const { default: j } = require("jscodeshift");

const canConvertToFunctionalComponent = (
  classDeclaration,
) => {
  /*
    해당 클래스가 하나의 render 메서드, state, props를 가지며
    refs, state, 혹은 다른 라이프사이클 메서드를 사용하지 않는지 확인한다.

    변환 대상이면 true를 반환하고, 
    그렇지 않으면 false를 반환한다.
  */

};

const convertClassToFunction = (
  classDeclaration,
) => {
  /*
    `canConvertToFunctionalComponent`에서 
    정의한 기준에 따라 해당 클래스 컴포넌트를 함수형 컴포넌트로
    변환한다.
  */
};

export default function (file, api) {
  const j = api.jscodeshift;
  const root = j(file.source);
  
  return root
    .find(j.ClassDeclaration)
    .filter((path) =>
      canConvertToFunctionalComponent(path.node),
    )
    .replaceWith((path) =>
      convertClassToFunction(path.node),
    )
    .toSource();
}
```

- 앞서 공유한 `SimpleComponent` 예시와 동등한 함수형 컴포넌트

```javascript
import React from "react";
import PropTypes from "prop-types";

function SimpleComponent({
  greeting = "Hello",
  name,
}) {
  return (
    <div>
      {greeting}, {name}!
    </div>
  );
}

export default SimpleComponent;
```
