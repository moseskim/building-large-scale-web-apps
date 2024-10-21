9 코드 조직화하기
===

## 9.1. 폴더와 파일 구조

- 루트 수준 디렉터리의 단순한 구조

```shell
src/
public/
tests/
build/
docs/
```

- `src/` 디렉터리의 콘텐츠를 정렬하는 방법의 예

```shell
src/
  components/
  pages/
  hooks/
  services/
  store/
  utils/
  assets/
  constants/
  types/
```

- 기능에 따라 `src` 디렉터리 안의 콘텐츠 정렬하기

```shell
src/
  features/
    Authentication/
      components/
      LoginForm/
        LoginForm.js
        LoginForm.module.css
      SignUpForm/
        SignUpForm.js
        SignUpForm.module.css
      hooks/
        useAuth.js
      services/
        authService.js
    UserProfile/
      components/
        ProfileCard/
          ProfileCard.js
          ProfileCard.module.css
        hooks/
          useUserProfile.js
        services/
          userService.js
    # ...
```

## 9.3. 배럴 익스포트

- `Authentication/components/` 디렉터리 안의 모든 컴포넌트 익스포트하기

```javascript
// .../Authentication/components/index.js
export { LoginForm } from './LoginForm';
export { SignUpForm } from './SignUpForm';
```

- 컴포넌트가 위치한 파일에서 해당 컴포넌트를 곧바로 임포트하기

```javascript
// .../Authentication/Authentication.jsx
import { LoginForm } from './components/LoginForm';
import { SignUpForm } from './components/SignUpForm';
```
- “배럴”로부터 임포트하기

```javascript
// .../Authentication/Authentication.jsx
import {
  LoginForm,
  SignUpForm,
} from "./components";
```



