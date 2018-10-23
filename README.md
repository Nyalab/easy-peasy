<p align='center'>
  <img src="https://i.imgur.com/KHTgPvA.png" width="320" />
</p>
<p align='center'>Easy peasy redux-powered state management</p>

[![npm](https://img.shields.io/npm/v/easy-peasy.svg?style=flat-square)](http://npm.im/easy-peasy)
[![MIT License](https://img.shields.io/npm/l/easy-peasy.svg?style=flat-square)](http://opensource.org/licenses/MIT)
[![Travis](https://img.shields.io/travis/ctrlplusb/easy-peasy.svg?style=flat-square)](https://travis-ci.org/ctrlplusb/easy-peasy)
[![Codecov](https://img.shields.io/codecov/c/github/ctrlplusb/easy-peasy.svg?style=flat-square)](https://codecov.io/github/ctrlplusb/easy-peasy)

```javascript
import { createStore } from 'easy-peasy';

const store = createStore({
  count: 1,
  inc: (state) => {
    state.count++
  }
});

store.dispatch.inc();

store.getState();
/*
{
  count: 2
}
*/
```

## Features

  - Quick to set up and use
  - Modify your state using simple mutations (which are converted to immutable operations under the hood)
  - Supports async actions to support needs such as data fetching
  - Full Redux Dev Tools Extension interop - see state changes for each action, debugging, etc
  - Outputs a Redux store, allowing easy integration with frameworks like React (e.g. via the `react-redux` package)

## TOCs

  - [Introduction](#introduction)
  - [Installation](#installation)
  - [Tutorial](#tutorial)
  - [Asynchronous Actions (Handling Effects)](#asynchronous-actions-handling-effects)
  - [Config](#config)
  - [Usage with React](#usage-with-react)
  - [API](#api)
    - [createStore](#createstore)
    - [effect](#effect)
    - [Action](#action)
    - [Effect Action](#effect-action)
  - [Prior Art](#prior-art)

## Introduction

Easy Peasy helps you to avoid all the boilerplate that is typical of a Redux implementation without losing the benefits of the Redux architecture and access to the wide set of tooling that is available to it.

## Installation

```bash
npm install easy-peasy
```

## Tutorial

Todo

## Asynchronous Actions (Handling Effects)

In order to do data fetching etc you can make use of the `effect` util to declare your action as being effectful.

Asynchronous actions cannot update state directly, however, they can dispatch standard actions in order to do so.

```javascript
import { effect } from 'easy-peasy';

const blog = {
  posts: {},
  fetchedPost: (state, post) => {
    state.posts[post.id] = post;
  },
  // Surround your action with the effect util
  //           👇
  fetchPost: effect(async (dispatch, payload) => {
    const response = await fetch(`/api/posts/${payload.id}`)
    const post = await response.json();
    // 👇 dispatch the result in order to update state
    dispatch.blog.fetchedPost(post);
  })
};
```

## Config

You can pass the following configuration options to `createStore`:

 - `devTools` (Boolean, default=false)

   Enable support for the [Redux Dev Tools Extension](https://github.com/zalmoxisus/redux-devtools-extension). Highly recommended for development.

Example Usage

```javascript
const store = easyPeasy(model, {
  devTools: true
})
```

## Usage with React

To use `easy-peasy` with React simply leverage the official [`react-redux`](https://github.com/reduxjs/react-redux) package.

### First, install the `react-redux` package

```bash
npm install react-redux
```

### Then wrap your app with the `Provider`

```javascript
import React from 'react';
import { render } from 'react-dom';
import { createStore } from 'easy-peasy';
import { Provider } from 'react-redux';
import model from './model';
import TodoList from './components/TodoList';

// 👇 create your store
const store = createStore({
  todos: {},
  addTodo: (state, text) => {
    // implementation...
  }
});

function App() {
  return (
    // 👇 then pass it to the Provider
    <Provider store={store}>
      <TodoList />
    </Provider>
  )
}

render(<App />, document.querySelector('#app'));
```

### Finally, use `connect` against your components

```javascript
import React, { Component } from 'react';
import { connect } from 'redux';


function TodoList({ todos, dispatch }) {
  //                        👆
  // When we connect the component, the dispatch will be attached with all the
  // available actions bound to it.
  return (
    <div>
      {todos.map(({id, text }) => <Todo key={id} text={text} />)}
      {/* Access actions via the dispatch 👇 */}
      <AddTodo onSubmit={dispatch.addTodo} />
    </div>
  )
}

// 👇 Map to your required state to your component via `connect`
export default connect(state => ({
  todos: state.todos
}))(EditTodo)
```

## API

Below is an overview of the API exposed by Easy Peasy.

### createStore

TODO

### Action

TODO

### Effect Action

TODO

## Prior art

This library was massively inspired by the following two awesome projects:

 - [rematch](https://github.com/rematch/rematch)

   Rematch is Redux best practices without the boilerplate. No more action types, action creators, switch statements or thunks.

 - [react-easy-state](https://github.com/solkimicreb/react-easy-state)

   Simple React state management. Made with ❤️ and ES6 Proxies.

 - [mobx-state-tree](https://github.com/mobxjs/mobx-state-tree)

   Model Driven State Management