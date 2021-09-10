# Using Redux with React

Now it's time to learn how to use Redux within a React application!

When you finish this article, you should be able to:

- Connect Redux to a React application
- Update a React function component to subscribe to a Redux store to listen for
  state changes
- Update a React function component to dispatch actions to a Redux store

## Integrating Redux into a React application

In general, the steps to integrate Redux into an existing React application are:

- Set up Redux
  - Install the `redux` and `react-redux` npm package
  - Define your actions
  - Define your reducer(s)
  - Create your store
  - Wrap the React application with the Redux provider and the created store
- Update components
  - Call the `useSelector` hook from `react-redux` package to retrieve parts of
    the Redux store state
  - Call the `useDispatch` hook from `react-redux` package to dispatch actions
    to the store

## React-Redux `Provider`

The React-Redux `Provider` component is a wrapper component that can give access
to the connected Redux store to any of its children. It's just like a provider
for a React context. In fact, the React-Redux `Provider` component is setting
up a context.

Remember, using a context avoids passing down props all the way from one part of
the component tree to a deeper component, or **prop threading**.

You can import and use the `Provider` component to wrap your `App` component and
set its `store` prop to your Redux `store`:

```js
import { Provider } from 'react-redux';
import configureStore from './store';

const store = configureStore();

function Root() {
  return (
    <Provider store={store}>
      <App />
    </Provider>
  );
}
```

`<Provider />` is simply a React component in which you wrap the rest of the
application. The `Provider` component receives the `store` as a `prop` and sets
a store context. Because you wrapped the entire `App` in the `Provider`
component, all your components can access the store context.

## `useSelector`

In a React function component, you can use the React-Redux hook, `useSelector`,
to consume the store context provided by the React-Redux `Provider`.

The `useSelector` hook accepts a function as an argument that will accept the
current or updated state of the Redux store. The return value of that function
is what is returned from the `useSelector` hook.

Here's an example of the `useSelector` hook being used in a React function
component:

```js
import { useSelector } from 'react-redux';

function FruitsList() {
  const fruits = useSelector((state) => state.fruitState);

  return (
    <ul>
      {fruits.map((fruit) => (
        <li key={fruit.id}>{fruit.name}</li>
      ))}
    </ul>
  );
}
```

The function passed into the `useSelector` hook will be subscribed to the store.
Whenever the state gets updated, the function will be invoked with the updated
state. If the return value of the function is different from the return value
from the previous state, the React function component that used the
`useSelector` hook will re-render.

The function passed into a `useSelector` hook is called a **selector**. If you
require the same information from the Redux store in multiple components, it may
be better to define a single selector function to use in those components.

In the following example, a selector function is defined then used inside of a
function component.

```js
export const getFruits = (state) => {
  return state.fruitState;
};
```

```js
import { getFruits } from '../store/fruitReducer';

function FruitsList() {
  const fruits = useSelector(getFruits);

  return (
    <ul>
      {fruits.map((fruit) => (
        <li key={fruit.id}>{fruit.name}</li>
      ))}
    </ul>
  );
}
```

To access the state inside of a function component and listen for changes to the
Redux store, all you need is the `useSelector` hook from the `react-redux`
package.

## `useDispatch`

To dispatch Redux actions within a React function component, the component needs
access to the `dispatch` method from the Redux store. The `useDispatch`
React-Redux hook will return the `dispatch` method when used in a function
component.

Remember, to update the Redux store, you must dispatch an action. Dispatching
an action may occur on a user input like a button click, as seen in the
following example.

```js
import { useDispatch } from 'react-redux';
import { addFruit } from '../store/fruitReducer';

function AddMelonButton() {
  const dispatch = useDispatch();

  return (
    <button onClick={() => dispatch(addFruit({ id: 5, name: 'melon' }))}>
      Melon
    </button>
  );
}
```

Or you can dispatch an action in a `useEffect`, like so:

```js
import { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { populateFruits } from '../store/fruitReducer';

function FruitsList() {
  const dispatch = useDispatch();
  const fruits = useSelector((state) => state.fruitState);

  useEffect(() => {
    dispatch(populateFruits());
  }, [dispatch]);

  return (
    <ul>
      {fruits.map((fruit) => (
        <li key={fruit.id}>{fruit.name}</li>
      ))}
    </ul>
  );
}
```

In this example, `dispatch` is added to the dependency array, but the `dispatch`
method should never change in a React application. Therefore, you can ignore
the `dispatch` in the dependency array when taking into account when the
`useEffect` function will be called.

## What you learned

In this article, you learned how to connect Redux to a React application using
the `Provider` component from the `react-redux` package. You also learned how to
access the Redux store state in a React function component using the
`useSelector` hook. Finally, you learned how to dispatch actions to the Redux
store within a React function component using the `useDispatch` hook.

[react-redux]: https://react-redux.js.org/
