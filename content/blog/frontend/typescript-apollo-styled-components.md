---
title: TypeScript + Apollo + Styled Components React 개발 환경 구축 (작성 중...)
date: 2019-11-22 16:11:81
category: frontend
description: 프론트엔드 프로젝트를 위해 자주 사용하는 기본 개발 환경 구축 Typescript, Apollo, Styled Components 를 이용한 React 튜토리얼 (작성 중..)
keywords: ['TypeScript', 'Apollo', 'Styled Components', '프론트엔드', '리액트']
---

## 기본 개발 환경 구축

```s
create-react-app sample-client --typescript
```

```s
yarn add apollo-boost graphql react-apollo
```

```ts
// apollo.ts

import ApolloClient, { Operation } from 'apollo-boost'

const client = new ApolloClient({
  uri: 'http://localhost:4000/graphql',
})

export default client
```

```ts
// index.tsx

import React from 'react'
import { ApolloProvider } from 'react-apollo'
import ReactDOM from 'react-dom'
import client from './apollo'

import App from './App'

ReactDOM.render(
  <ApolloProvider client={client}>
    <App />
  </ApolloProvider>,
  document.getElementById('root')
)
```

```ts
import ApolloClient, { Operation } from 'apollo-boost'

const client = new ApolloClient({
  clientState: {
    defaults: {
      auth: {
        __typename: 'Auth',
        isLoggedIn: Boolean(localStorage.getItem('authToken')),
      },
    },
    resolvers: {
      Mutation: {
        logUserIn: (_, { token }, { cache }) => {
          localStorage.setItem('authToken', token)
          cache.writeData({
            data: {
              auth: {
                __typename: 'Auth',
                isLoggedIn: true,
              },
            },
          })
          return null
        },
        logUserOut: (_, __, { cache }) => {
          localStorage.removeItem('authToken')
          cache.writeData({
            data: {
              auth: {
                __typename: 'Auth',
                isLoggedIn: false,
              },
            },
          })
          return null
        },
      },
    },
  },
  request: async (operation: Operation) => {
    operation.setContext({
      headers: {
        'X-User-Token': localStorage.getItem('authToken') || '',
      },
    })
  },
  uri: 'http://localhost:4000/graphql',
})

export default client
```

```

```

### Reference

- https://velog.io/@zansol/%EC%A3%BC%EB%8B%88%EC%96%B4%ED%83%88%EC%B6%9C%EA%B8%B0-Apollo-Client-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0
- https://www.daleseo.com/graphql-react-apollo-client/
- https://medium.com/@han7096/graphql-%EA%B3%BC-apollo%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EB%A9%B0-%EC%A4%91%EA%B0%84-%EC%A0%95%EB%A6%AC-42981522b188
