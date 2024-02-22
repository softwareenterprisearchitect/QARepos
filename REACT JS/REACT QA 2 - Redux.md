Mostly Asked Redux Interview Question and Answers


Redux interview questions and answers
First, let's see mostly asked redux interview questions and then we will cover all the questions one by one. For in-depth knowledge of a particular topic, you can click on the link attached with the question.

1. What is Redux?
2. What are the core principles of Redux?
3.What is the difference between mapStateToProps() and mapDispatchToProps()?
4. Can I dispatch an action in the reducer?
5. How to access the Redux store outside a component?
6. How to dispatch an action on load?
7. How to use connect from React Redux?
8. How to reset the state in Redux?
9. How to make an AJAX request in Redux?
10. What is the purpose of the constants in Redux?
11. How to structure Redux top-level directories?
12. What is Redux Thunk?
13. What is redux-saga?
14. What are the differences between redux-saga and redux-thunk
15. What are the differences between call() and put() in redux-saga?
16. How to add multiple middleware to Redux?
17. How to set the initial state in Redux?

1. What is Redux?

Redux is an open-source JavaScript library for managing the state of an application. It provides a predictable state container and a set of rules for modifying that state, which helps to simplify and streamline the process of building complex and interactive user interfaces. Redux is commonly used in conjunction with React, but it can also be used with other frameworks or libraries.

2. What are the core principles of Redux?

The core principles of Redux are:

a)Single source of truth: The state of an entire application is stored in a single store object.

b)State is read-only: The only way to change the state is by emitting an action, an object describing what happened.

c)Changes are made with pure functions: To specify how the state tree is transformed by actions, you write pure reducers.

d)Changes are made with pure functions: To specify how the state tree is transformed by actions, you write pure reducers.

e)Time-travel debugging: You can trace every action that has occurred in the application and see how the state has changed over time.

By following these principles, Redux provides a simple and predictable way to manage the state of an application, making it easier to reason about and test.

3.What is the difference between mapStateToProps() and mapDispatchToProps()?

mapStateToProps() and mapDispatchToProps() are both functions used in React-Redux to connect the Redux state and actions to React components.

mapStateToProps() is a function that maps a slice of the Redux state to the props of a React component. It takes the current Redux state as its argument and returns an object that maps the relevant state data to the props of the component. This allows the component to access the relevant data from the Redux store.

mapDispatchToProps() is a function that maps Redux actions to the props of a React component. It takes a dispatch function as its argument and returns an object that maps the relevant actions to the props of the component. This allows the component to dispatch the relevant actions to update the state in the Redux store.

In summary, mapStateToProps() connects the Redux state to the props of a React component, while mapDispatchToProps() connects Redux actions to the props of a React component.

4. Can I dispatch an action in the reducer?

Technically, you can dispatch an action in the reducer, but it is not recommended and is generally considered an anti-pattern in Redux.

The reducer function should be a pure function, which means that it should not have any side effects or cause any mutations to the state or the application. The purpose of the reducer is to take in the current state and an action and return a new state based on the action.

If you dispatch an action inside the reducer, it can lead to unexpected behavior and make it difficult to debug and reason about the state changes in your application. Additionally, it can lead to an infinite loop, as dispatching an action will cause the reducer to be called again, which can in turn dispatch more actions, and so on.

In general, actions should be dispatched from the components or from middleware, rather than from the reducer.

5. How to access the Redux store outside a component?

To access the redux store outside a react component, Redux connect the function works great for regular React components.

The examples below show how to access a JWT token from the Redux store.

Option 1: Export the Store

import { createStore } from 'redux'
import reducer from './reducer'

const store = createStore(reducer)

export default store
Here, we are creating the store and export it. This will make it available to other files. Here we’ll see an api the file making a call where we need to pass a JWT token to the server:

import store from './store'

export function getProtectedThing() {
  // grab current state
  const state = store.getState()

  // get the JWT token out of it
  // (obviously depends on how your store is structured)
  const authToken = state.currentUser.token

  // Pass the token to the server
  return fetch('/user/thing', {
    method: 'GET',
    headers: {
      Authorization: `Bearer ${authToken}`
    }
  }).then(res => res.json())
}
Option 2: Pass the value from a React Component

It’s simple to get access to the store inside a React component — no need to pass the store as a prop or import it, just use the connect() function from React Redux, and supply a mapStateToProps() a function that pulls out the data.

import React from 'react'
import { connect } from 'react-redux'
import * as api from 'api'

const ItemList = ({ authToken, items }) => {
  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>
          {item.name}
          <button
            onClick={
              () => api.deleteItem(item, authToken)
            }>
            DELETE THIS ITEM
          </button>
        </li>
      )}
    </ul>
  )
}

const mapStateToProps = state => ({
  authToken: state.currentUser && state.currentUser.authToken,
  items: state.items
})

export connect(mapStateToProps)(ItemList)
6. How to dispatch an action on load?

For class component, you can dispatch an action in componentDidMount() method and in render() the method you can verify the data.

/**
 * Dispatch an action on load
 */
class App extends Component {
  componentDidMount() {
    this.props.fetchData()
  }

  render() {
    return this.props.isLoaded
      ? <div>{'Loaded'}</div>
      : <div>{'Not Loaded'}</div>
  }
}

const mapStateToProps = (state) => ({
  isLoaded: state.isLoaded
})

const mapDispatchToProps = { fetchData }

export default connect(mapStateToProps, mapDispatchToProps)(App)
For functional component, you can dispatch an action on load by using the useEffect hook with an empty dependency array ([]) as the second argument.

import { useEffect } from 'react';
import { useDispatch } from 'react-redux';
import { myActionCreator } from './actions';

function MyComponent() {
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(myActionCreator());
  }, []);

  // rest of the component code
}
In this example, the useEffect hook is used to call the dispatch function with the myActionCreator() action when the component is first loaded. The empty dependency array ensures that the effect only runs once, on the mount, and not on subsequent re-renders.

Note that you’ll need to import the useEffect and useDispatch hooks from the react and react-redux packages respectively. Additionally, you'll need to define the myActionCreator() function in a separate file with the action type and payload.

7. How to use connect from React Redux?

The connect() function connects a React component to a Redux store. It provides its connected component with the pieces of the data it needs from the store, and the functions it can use to dispatch actions to the store.

It does not modify the component class passed to it; instead, it returns a new, connected component class that wraps the component you passed in.

Use mapStateToProps(): It maps the state variables from your store to the props that you specify.
Connect props to container: The object returned by the mapStateToProps function is connected to the container.
import React from 'react'
import { connect } from 'react-redux'

class App extends React.Component {
  render() {
    return <div>{this.props.containerData}</div>
  }
}

function mapStateToProps(state) {
  return { containerData: state.data }
}

export default connect(mapStateToProps)(App)
8. How to reset the state in Redux?

To reset the state in Redux, you can define a new action and a corresponding reducer that returns the initial state of your application.

Here’s an example:

// Define a reset action type
const RESET_STATE = 'RESET_STATE';

// Define an action creator function for the reset action
export const resetState = () => ({
  type: RESET_STATE
});

// Define a reducer that handles the reset action
const rootReducer = (state, action) => {
  if (action.type === RESET_STATE) {
    return initialState; // replace initialState with your initial state object
  }
  return state;
};
In this example, the resetState() action creator function returns an action object with the RESET_STATE type. The rootReducer function checks if the incoming action has the RESET_STATE type, and if so, returns the initial state of the application.

You can then dispatch the resetState() action from your components or other parts of your application to reset the state of the Redux store.

Note that this approach assumes that your application has a static initial state object that can be used to reset the state. If your initial state depends on some dynamic values or external sources, you may need to handle the reset logic differently.

9. How to make an AJAX request in Redux?

There are three most widely used and stable Redux Ajax middleware are:

Redux Promise Middleware
Redux Thunk Middleware
Redux Saga Middleware
Redux Promise Middleware: This is the most simple way of doing Ajax calls with Redux. When using Redux Promise, your action creator can return a Promise inside the Action.

function getUserName(userId) {
    return {
        type: "SET_USERNAME",
        payload: fetch(`/api/personalDetails/${userId}`)
                .then(response => response.json())
                .then(json =>  json.userName)
    }
}
This middleware automatically dispatches two events when the Ajax call succeeds: SETUSERNAMEPENDING and SETUSERNAMEFULFILLED. If something fails it dispatches SETUSERNAMEREJECTED.

When to use:

You want the simplest thing with minimum overhead
You prefer convention over configuration
You have simple Ajax requirements
Redux Thunk Middleware: This is the standard way of doing Ajax with Redux. When using Redux Thunk, your action creators returns a function that takes one argument dispatch:

function getUserName(userId) {
    return dispatch => {
        return fetch(`/api/personalDetails/${userId}`)
        .then(response => response.json())
        .then(json => dispatch({ type: "SET_USERNAME", userName: json.userName })
    }
}
The action creator can call dispatch inside .then to execute it asynchronously. The action creator can call dispatch as many time as it wants.

When to use:

You make many Ajax calls in one action, and need to dispatch many actions
You require full control of the format of your actions
Redux Saga Middleware: This is the most advanced way of doing Ajax with Redux. It uses an ES6 feature called generators. When using Redux Saga you do your Ajax calls in a saga instead of an action creator. This is how a saga looks like:

import { call, put, takeEvery } from 'redux-saga/effects'

// call getUserName when action SET_USERNAME is dispatched
function* mySaga() {
  yield takeEvery("SET_USERNAME", getUserName);
}

function* getUserName(action) {
   try {
      const user = yield call(fetch, `/api/personalDetails/${userId}`);
      yield put({type: "SET_USERNAME_SUCCEEDED", user: user});
   } catch (e) {
      yield put({type: "SET_USERNAME_FAILED", message: e.message});
   }
}

export default mySaga
Here, sagas listen to actions that you dispatch as regular synchronous actions. In this case, the saga getUserName is executed when the action SET_USERNAME is dispatched. The * next to the function means it's a generator and yield is a generator keyword.

When to use:

You need to be able to test the asynchronous flow easily
You are comfortable working with ES6 Generators
You value pure functions
10. What is the purpose of the constants in Redux?

that are used in multiple places within an application. The purpose of constants is to provide a centralized and consistent way of referring to these values throughout the application, which helps to prevent errors and make the code easier to maintain.

For example, you might define a constant for the action type of a “ADD_TODO” action:

export const ADD_TODO = 'ADD_TODO';
You can then use this constant in your action creators, reducers, and other parts of your application that need to refer to the “ADD_TODO” action:

import { ADD_TODO } from './constants';

export const addTodo = (text) => ({
  type: ADD_TODO,
  payload: {
    text
  }
});
Using constants in this way helps to ensure that all parts of the application are referring to the same value for the “ADD_TODO” action type, which can prevent errors caused by typos or inconsistencies.

In addition to action types, constants can also be used to define other values that are used in multiple places within an application, such as API endpoint URLs or configuration settings. By defining these values as constants, you can ensure that they are consistent throughout the application and can be easily updated if needed.

11. How to structure Redux top-level directories?

Most of the applications have several top-level directories as below:

Components — Contains all ‘dumb’ or presentational components, consisting only of HTML and styling.
Containers — Contains all corresponding components with logic in them. Each container will have one or more components depending on the view represented by the container.
Actions — All Redux actions
Reducers — All Redux reducers
API — API connectivity-related code. Handler usually involves setting up an API connector centrally with authentication and other necessary headers.
Utils — Other logical codes that are not React-specific. For example, authUtils would have functions to process the JWT token from the API to determine the user scopes.
Store — Used for redux store initialization.
└── src
    ├── actions
    │   ├── articleActions.js
    │   ├── categoryActions.js
    │   └── userActions.js
    ├── api
    │   ├── apiHandler.js
    │   ├── articleApi.js
    │   ├── categoryApi.js
    │   └── userApi.js
    ├── components
    │   ├── ArticleComponent.jsx
    │   ├── ArticleListComponent.jsx
    │   ├── CategoryComponent.jsx
    │   ├── CategoryPageComponent.jsx
    │   └── HomePageComponent.jsx
    ├── containers
    │   ├── ArticleContainer.js
    │   ├── CategoryPageContainer.js
    │   └── HomePageContainer.js
    ├── index.js
    ├── reducers
    │   ├── articleReducer.js
    │   ├── categoryReducer.js
    │   └── userReducer.js
    ├── routes.js
    ├── store.js
    └── utils
        └── authUtils.js
12. What is Redux Thunk?

Redux Thunk is a middleware for Redux that allows you to write asynchronous logic in your Redux actions. It provides a way to dispatch actions that can be asynchronous, such as AJAX requests, and delay the dispatching of other actions until the asynchronous logic has been completed.

Normally, Redux actions are plain objects that represent an intention to change the state of the application. However, with Redux Thunk, you can also define actions that are functions that take dispatch and getState as arguments. These functions can perform asynchronous operations and dispatch other actions once the operation has been completed.

Here’s an example of a Redux action that uses Redux Thunk to make an AJAX request:

import { fetchPostsRequest, fetchPostsSuccess, fetchPostsFailure } from './actions';

export const fetchPosts = () => {
  return (dispatch) => {
    dispatch(fetchPostsRequest());

    return fetch('https://jsonplaceholder.typicode.com/posts')
      .then(response => response.json())
      .then(posts => {
        dispatch(fetchPostsSuccess(posts));
      })
      .catch(error => {
        dispatch(fetchPostsFailure(error.message));
      });
  };
};
In this example, the fetchPosts() action creator returns a function that takes dispatch as an argument. The function dispatches a fetchPostsRequest() action to indicate that the request is in progress, and then uses the fetch API to make an AJAX request. If the request succeeds, the function dispatches a fetchPostsSuccess() action with the retrieved posts as the payload. If the request fails, the function dispatches a fetchPostsFailure() action with the error message as the payload.

To use Redux Thunk in your Redux store, you need to include it as middleware when you create the store:

import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers';

const store = createStore(rootReducer, applyMiddleware(thunk));
With Redux Thunk, you can write more complex and asynchronous logic in your Redux actions, which can help to simplify your application and make it more robust.

13. What is redux-saga?

Redux Saga is a middleware for Redux that allows you to write complex and asynchronous logic in your Redux applications. It provides a way to manage side effects, such as AJAX requests and animations, in a declarative and testable way.

Redux Saga uses a combination of ES6 Generators and Redux to make asynchronous logic easier to read, write, and test. With Redux Saga, you can write sagas that listen for specific actions and perform a series of steps when those actions occur. Sagas are written as generator functions that use a set of special yield statements to control the flow of the asynchronous logic.

Here’s an example of a Redux Saga that performs an AJAX request and dispatches other actions based on the response:

import { call, put, takeEvery } from 'redux-saga/effects';
import { FETCH_POSTS_REQUEST, fetchPostsSuccess, fetchPostsFailure } from './actions';

function* fetchPosts() {
  try {
    const response = yield call(fetch, 'https://jsonplaceholder.typicode.com/posts');
    const posts = yield response.json();
    yield put(fetchPostsSuccess(posts));
  } catch (error) {
    yield put(fetchPostsFailure(error.message));
  }
}

function* watchFetchPosts() {
  yield takeEvery(FETCH_POSTS_REQUEST, fetchPosts);
}

export default function* rootSaga() {
  yield all([
    watchFetchPosts()
  ]);
}
In this example, the fetchPosts() saga is a generator function that performs an AJAX request using the fetch API. The call function is used to call the fetch API, and the yield keyword is used to wait for the response. If the request is successful, the fetchPostsSuccess() action is dispatched using the put function. If the request fails, the fetchPostsFailure() action is dispatched instead.

The watchFetchPosts() saga listens for FETCH_POSTS_REQUEST actions using the takeEvery function, and runs the fetchPosts() saga whenever one of those actions occurs.

Finally, the rootSaga() generator function uses the all function to combine all of the sagas in the application into a single generator function that can be passed to the Redux store.

Redux Saga provides a powerful and flexible way to manage asynchronous and side-effect heavy logic in your Redux applications, and can help to simplify and organize your code. However, it does require a bit of additional setup and can have a steeper learning curve than other Redux middleware like Redux Thunk.



In this example, the fetchPosts() saga is a generator function that performs an AJAX request using the fetch API. The call function is used to call the fetch API, and the yield keyword is used to wait for the response. If the request is successful, the fetchPostsSuccess() action is dispatched using the put function. If the request fails, the fetchPostsFailure() action is dispatched instead.

The watchFetchPosts() saga listens for FETCH_POSTS_REQUEST actions using the takeEvery function, and runs the fetchPosts() saga whenever one of those actions occurs.

Finally, the rootSaga() generator function uses the all function to combine all of the sagas in the application into a single generator function that can be passed to the Redux store.

Redux Saga provides a powerful and flexible way to manage asynchronous and side-effect-heavy logic in your Redux applications and can help to simplify and organize your code. However, it does require a bit of additional setup and can have a steeper learning curve than other Redux middleware like Redux Thunk.

14. What are the differences between redux-saga and redux-thunk

Redux Saga and Redux Thunk are both middleware for Redux that allow you to handle side effects and perform asynchronous operations in your Redux applications. However, they differ in their approach and capabilities:

Approach: Redux Thunk is based on function composition, while Redux Saga is based on ES6 Generators.

Flow control: Redux Thunk allows you to dispatch functions that return other functions or Promises, while Redux Saga uses a series of yield statements to control the flow of asynchronous operations.

Testability: Redux Thunk can be easier to test, as it is based on synchronous functions and does not require mocking or mocking a generator runtime. On the other hand, Redux Saga requires a bit more setup to test, as it uses generators and can involve mocking.

Complexity: Redux Saga is more powerful and flexible than Redux Thunk, allowing you to handle more complex and intricate side effects, such as race conditions and parallel operations.

In general, if your application requires complex or long-running asynchronous operations, or you need more fine-grained control over the flow of those operations, Redux Saga may be the better choice. If your application has simpler asynchronous requirements or you prefer a more straightforward and functional approach, Redux Thunk may be the better choice. Ultimately, the choice between the two middleware comes down to the specific requirements and constraints of your application.

15.What are the differences between call() and put() in redux-saga?

call() and put() are both effects that can be used in Redux Saga to manage asynchronous operations, but they have different purposes:

call() is used to call a function that returns a Promise or an iterator. It allows you to make asynchronous calls in a synchronous way, as the generator function will wait for the Promise or iterator to resolve before continuing. This can be useful for making API calls or other long-running operations.

Here’s an example of using call() to make an API call:

import { call } from 'redux-saga/effects';
import api from './api';

function* mySaga() {
  const data = yield call(api.getData);
  // data contains the result of the API call
}
In this example, api.getData() is a function that returns a Promise, and call() is used to wait for the Promise to resolve before assigning the result to the data variable.

put() is used to dispatch a Redux action. It allows you to update the application state in response to an event or action and can trigger additional sagas to run. This can be useful for updating the UI, triggering side effects, or handling errors.

Here’s an example of using put() to dispatch an action:

import { put } from 'redux-saga/effects';
import { fetchDataSuccess, fetchDataError } from './actions';
import api from './api';

function* mySaga() {
  try {
    const data = yield call(api.getData);
    yield put(fetchDataSuccess(data));
  } catch (error) {
    yield put(fetchDataError(error));
  }
}
In this example, fetchDataSuccess() and fetchDataError() are action creators that return Redux actions. If the API call is successful, put() is used to dispatch the fetchDataSuccess() action with the result of the API call. If the API call fails, put() is used to dispatch the fetchDataError() action with the error object.

Overall, call() and put() are both important effects in Redux Saga that are used for different purposes. While call() is used to manage asynchronous calls, put() is used to update the Redux state and trigger additional sagas.

16. How to add multiple middlewares to Redux?

In Redux, you can use the applyMiddleware() function to add multiple middleware functions to the store. The applyMiddleware() function takes one or more middleware functions as arguments, and returns a function that can be used to create a Redux store.

Here’s an example of how to add multiple middleware functions to Redux:

import { createStore, applyMiddleware } from 'redux';
import logger from 'redux-logger';
import thunk from 'redux-thunk';
import createSagaMiddleware from 'redux-saga';
import rootReducer from './reducers';
import rootSaga from './sagas';

const sagaMiddleware = createSagaMiddleware();

const middlewares = [logger, thunk, sagaMiddleware];

const store = createStore(
  rootReducer,
  applyMiddleware(...middlewares),
);

sagaMiddleware.run(rootSaga);

export default store;
In this example, we’re using the createStore() function from Redux to create a store, and passing it the rootReducer which combines all of the reducers in our application. We're then using the applyMiddleware() function to apply multiple middleware functions to the store. These include the logger middleware, the thunk middleware, and a sagaMiddleware that we created using the createSagaMiddleware() function. We pass all of these middleware functions as an array to the applyMiddleware() function using the spread operator.

Finally, we use the sagaMiddleware.run() function to run our rootSaga, which is a generator function that defines all of our sagas.

By applying multiple middleware functions to the store in this way, we can enhance the capabilities of Redux and add additional functionality to our application, such as handling asynchronous actions, logging, and more.

17. How to set the initial state in Redux?

In Redux, the initial state of the store is typically defined when you create the reducer function. The initial state should be an object that represents the starting state of your application.

Here’s an example of how to set the initial state in a Redux reducer:

const initialState = {
  counter: 0,
  todos: [],
};

function reducer(state = initialState, action) {
  switch (action.type) {
    case 'INCREMENT':
      return {
        ...state,
        counter: state.counter + 1,
      };
    case 'ADD_TODO':
      return {
        ...state,
        todos: [...state.todos, action.payload],
      };
    default:
      return state;
  }
}
In this example, we’re defining an initialState object that contains a counter property with a value of 0 and a todos property with an empty array. We're then using this initialState object as the default value for the state parameter of our reducer function.

When an action is dispatched to the reducer, the state parameter will be set to the current state of the store. If the state parameter is undefined (i.e., the store is being initialized for the first time), the default value of initialState will be used.

By setting the initial state in this way, you can ensure that your application always starts with a known state and that it behaves consistently, regardless of the actions that are dispatched.

