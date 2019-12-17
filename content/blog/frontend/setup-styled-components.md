---
title: <React Typescript Setup Tutorial> 2. Styled Components 세팅하기
date: 2019-12-17 18:12:82
category: frontend
description: 이번에는 React Typescript 환경에서 Styled Components를 세팅해보자.
keywords: ['TypeScript', 'React', 'Styled Components', '프론트엔드', '리액트']
---

## Styled Components

Styled Components는 대표적인 CSS-in-JS 라이브러리이다. 따로 CSS 파일을 두지 않고 React Component 내에 Javascript와 같이 스타일링 코드를 적용한다.

예를들면 다음과 같다.

```typescript
import React from 'react'
import styled from 'styled-components'

function TodoList() {
  return <TodoListBox>/* 생략 */</TodoListBox>
}

const TodoListBox = styled.div`
  position: relative;
  height: 100%;
  background-color: ${props => props.theme.color.main};
  overflow-y: scroll;
  min-width: ${props => props.theme.basicWidth};
`
```

최근들어 React 프로젝트 대부분 적용되는 방식이며 이렇게 CSS-in-JS 방식을 이용하면 React Native 와도 스타일링 코드를 대부분 공유할 수 있다는 장점이 있다.

특히 나처럼 CSS 코드 초보인 사람은 CSS 파일에 스타일링하다보면 그 난해함에 난감한 경우(중첩되거나 덮어씌워지거나 등등)가 많은데 분리하여 그 컴포넌트에만 집중하여 스타일링을 하니 네이밍을 정하는 것도 간단하고 쉽게 스타일링을 할 수 있어서 좋은 것 같다.

대신, React Typescript 프로젝트에 적용하려면 그 전에 몇가지 해주어야 할 세팅이 있는데 그에 대해서 간단히 정리해보았다.

- [Styled Components 5분 소개](https://orezytivarg.github.io/a-5-minute-intro-to-styled-components/)
- [Styled Components – 스타일을 품은 컴포넌트](https://hudi.kr/styled-components-%EC%8A%A4%ED%83%80%EC%9D%BC%EC%9D%84-%ED%92%88%EC%9D%80-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8/)

## Setup

```s
yarn add styled-components @types/styled-components
```

기존에 styled-components 라이브러리 추가 후에 Theme 을 활용하기 위해 따로 styled-components.ts 파일을 만들어서 선언을 해주었어야 했는데 @types/styled-components 가 추가된 이후로는 그럴 필요가 없고 직접 추가를 해주면 되는 것 같다. React Native에서는 이전 방식대로 해주어야 하는 것 같으니 참고하자.

- [Styled Components Typescript Api Doc 참고](https://www.styled-components.com/docs/api#typescript)

총 세가지 파일을 만들건데 나는 src/ 경로에 styles 라는 폴더를 따로 만들고 모아두고 활용한다.

- src/styles/styled.d.ts
- src/styles/theme.ts
- src/styled/global-styles.ts

## styles/styled.d.ts

```typescript
// src/styles/styled.d.ts
import 'styled-components';

declare module 'styled-components' {
  export interface DefaultTheme {
    basicWidth: string;

    color {
      main: string;
      sub: string;
    }
  }
}
```

Typescript에는 module을 *declare module*을 이용하여 extend하는 방법이 존재한다.

해당 방법을 이용하면 코드가 합쳐지는데 Style Components 에서 제공되는 ThemeProvider를 활용하기 위해서 interface를 추가해주는 것이다.

이 테마 선언 기능은 혼자서 작업하거나 초반에는 그렇게 쓸모가 없지만 컴포넌트가 복잡해지거나 잘 기획된 디자인 시스템(테마/프리셋)을 바탕으로 개발한다면 자주 사용되는 요소들 색깔부터 길이까지 정의를 해놓으면 props로 바로 쓸수가 있다.

기존의 sass의 mixin 변수와 비슷한 기능을 하는데 요즈음 특히 다크 모드 등 다양한 테마를 지원해야 하는 환경에서 특히 유용하게 활용될 수 있을 것 같다.

## styles/theme.ts

```typescript
// src/styles/theme.ts
import { DefaultTheme } from 'styled-components';

const theme: DefaultTheme = {
  basicWidth: '320px';

    color {
      main: '#1c1f25';
      sub: '#fff';
    }
};

export { theme };
```

앞에서 정의한 테마 interface의 실제 테마를 구현하는 곳이다.

지금은 프로젝트 초반이라 테마가 하나밖이라 그냥 theme이라고 변수로 두었는데 나중에 basicTheme, darkTheme 이런 식으로 각각에 맞는 테마 구현체를 두고 export를 해서 사용할 수 있을 것이다.

해당 theme은 ThemeProvider를 이용해서 적용할 수 있는데 여기서는 App Component에 적용해보았다.

```typescript
// src/App.tsx
import React from 'react'
import { Route, Switch } from 'react-router-dom'
import MainPage from './pages/MainPage'
import AboutPage from './pages/AboutPage'
import { theme } from './styles/theme'
import { ThemeProvider } from 'styled-components'

function App() {
  return (
    <ThemeProvider theme={theme}>
      <Switch>
        <Route exact={true} path="/" component={MainPage} />
        <Route exact={true} path="/about" component={AboutPage} />
      </Switch>
    </ThemeProvider>
  )
}

export default App
```

## styles/global-styles.ts

먼저 전역적인 Global Style을 지정하기 전에 normalize.css를 적용해줄 예정이다.

각 브라우저마다 기본적으로 들어가는 스타일링이 있어서 그것에 따라 브라우저마다 약간의 차이를 보이는데 CSS 코드가 복잡해지면 그 차이가 심하게 두드러지는 경우가 많다.

특히 디자인이라는 것이 미묘한 차이가 큰 차이로 나타나기 때문에 이걸 통일 시켜주는 것이 좋은데 그럴때 reset.css 또는 normalize.css를 쓰는 것이 좋다.

둘의 차이점은 아래 링크를 통해 확인해보고 여기서는 normalize.css를 활용해볼 것이다.

- [CSS - Resetting VS Normalize](https://tuhbm.github.io/2018/02/21/cssReset/)
- [reset.css 와 Normalize.css](https://sapjil.net/resetcss-normalizecss/)

찾다보니 normalize.css를 직접 import 하지 않고 [styled-normalize](https://github.com/sergeysova/styled-normalize) 라이브러리를 이용하여 createGlobalStyle API를 이용하여 프로젝트 전체의 스타일링을 지정해줄 것이다.

```s
yarn add styled-normalize
```

```typescript
// src/styles/global-styles.ts
import { createGlobalStyle } from 'styled-components'
import { normalize } from 'styled-normalize'

export const GlobalStyle = createGlobalStyle`
  ${normalize}

  html,
  body {
    overflow: hidden;
  }

  * {
    box-sizing: border-box;
  }
`
```

이렇게 정의된 스타일을 index.tsx 에서 컴포넌트처럼 적용하면 된다.

```typescript
// src/index.tsx
import React from 'react'
import ReactDOM from 'react-dom'
import { BrowserRouter } from 'react-router-dom'
import App from './App'
import { GlobalStyle } from './styles/global-styles'

ReactDOM.render(
  <BrowserRouter>
    <GlobalStyle />
    <App />
  </BrowserRouter>,
  document.getElementById('root')
)
```

## 적용

앞에서 본 TodoList 컴포넌트이다.

```typescript
// src/components/TodoList.tsx
import React from 'react'
import styled from 'styled-components'

function TodoList() {
  return <TodoListBox>/* 생략 */</TodoListBox>
}

const TodoListBox = styled.div`
  position: relative;
  height: 100%;
  background-color: ${props => props.theme.color.main};
  overflow-y: scroll;
  min-width: ${props => props.theme.basicWidth};
`
```

## 마무리하며

React Typescript 환경에서 간단히 Styled Components를 적용하는 작업을 해보았다.

컴포넌트가 복잡해지면 잘 분리해서 각각의 스타일링을 적용해주면 기존에 CSS 코드 때문에 헤매는 일이 많이 줄어들 것이다.

예전 Legacy 프로젝트들이 대부분 SASS를 활용한 스타일링 시스템이라서 코드 작업을 하면서 마이그레이션하는 것에 조금 애먹고있긴 한데 한번 세팅하고 옮기기 시작하니 생각보다 생산성이 좋다. 특히 분리되어 있으니 컴포넌트 내에서만 중복되지 않으면 네이밍에 그렇게 힘쓰지 않아도 된다는 점도 마음에 든다.

역시 프로그래밍 생산성의 길은 잘 분리하고 일반화하는 것에 있는 것 같다.
