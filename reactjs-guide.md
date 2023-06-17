# React Guide

[TOC]

### Folder structure

```
- /src
    - /components
    - /images
    - /redux
        - /actions
        - /reducers
        - /selectors
        - /utils
        - store.js
    - /routes
    - /styles
    - /utility
    - App.js
    - index.js
```

### React Hooks

#### State Hook

```js
import {useState} from "react";

const [state, setState] = useState("");
```

#### Lifecycle method hooks

- Component did mount

```js
componentDidMount() {
    console.log('I am mounted!');
}

useEffect(() => console.log('mounted'), []);
```

- Component did update

```js
componentDidUpdate(prevProps){
   if(prevProps.myName !== props.myName){
      // .. do something
   }
}

useEffect(() => {
   // .. do something
}, [myName]); // if myName changes, run effect
```

- Component will unmount

```js
componentWillUnmount() {
    console.log('will unmount');
}

useEffect(() => {
      // some other things...
    return () => {
       // clean up function 
        console.log('will unmount');
    }
}, []);
```

## React Router

Reference: [Link](https://medium.com/the-andela-way/understanding-the-fundamentals-of-routing-in-react-b29f806b157e)

```jsx
import { BrowserRouter as Router, Route, Switch } from "react-router-dom";

<Router>
   <Navbar />
   <Switch>
      <Route path="/">
         <Home />
      </Route>
   </Switch>
   <Footer />
</Router>
```

#### Hooks

Reference: 

- [css tricks](https://css-tricks.com/the-hooks-of-react-router/)
- [dev.to](https://dev.to/developer_buddy/react-router-dom-hooks-how-to-use-them-3a93)

```js
import { useHistory, useLocation, useParams } from 'react-router-dom';

const history = useHistory();
const location = useLocation(); // similar to window.location
const { name } = useParams(); // /profile/:name
location.pathname
history.push("/profile"); 
history.goBack();
```

#### Nested Routing

```jsx
import { useRouteMatch, Route, Link } from 'react-router-dom';

function Auth() {
  const {url, path} = useRouteMatch();
  return (
    <>
     <Link to={`${url}/login`}>Dashboard</Link>
     <Link to={`${url}/register`}>add menu item</Link>
      <Route path={`${path}/login`}>
        <Login />
      </Route>
      <Route path={`${path}/register`}>
        <Register />
      </Route>
    </>
  );
}
```

#### Private Routes

```jsx
import React from "react";
import { Route, Redirect } from "react-router-dom";
import { useSelector } from "react-redux";

const PrivateRoute = ({ component: Component, ...rest }) => {
   const isAuth = useSelector(state => state.auth.isAuthenticated);
   // console.log(auth);
   return (
      <Route
         {...rest}
         render={props =>
            isAuth === true ? (
               <Component {...props} />
            ) : (
               <Redirect to="/login" />
            )
         }
      />
   );
};

export default PrivateRoute
```

#### Links and Navlinks

```jsx
import { Link, NavLink } from "react-router-dom";

<Link to="/items">Items</Link>
<NavLink to="/about">About</NavLink> // has active class
```

## Redux Setup

```bash
npm i redux react-redux
```

Reference: [Link](https://reactgo.com/react-redux-hooks/)

### Create Store

```javascript
// store.js
import { createStore, applyMiddleware, compose } from "redux";
import thunk from "redux-thunk";
import allReducers from "./reducers/allReducers";

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;
const store = createStore(
    allReducers,
    composeEnhancers(applyMiddleware(thunk))
);

// const store = createStore(allReducers, applyMiddleware(thunk));

export default store;
```

### Using Store

```jsx
import { Provider } from "react-redux";

<Provider store={store}>
   <App />
</Provider>
```

### Create Reducer

```js
// reducers
const initialState = {
    name: "",
   list: []
};

const myReducer = (state = initialState, action) => {
    switch (action.type) {
        case "ADD_NAME":
            return {
                ...state,
                name: action.payload
            };

        default:
            return state;
    }
};

export default myReducer;
```

```js
// allReducers
import { combineReducers } from "redux";
import myReducer from "./myReducer";

const allReducers = combineReducers({
    my: myReducer,
});

// state.my has state for that reducer

export default allReducers;
```

### Create Action

```js
export function setName(name) {
    return {
        type: "ADD_NAME",
        payload: name,
    };
}

// async actions
export function fetchData(){
   return async dispatch => {
      try{
         let res = await fetch("...");
         let json = res.json();
         dispatch(someAction())
      }catch(err){
         console.log(err)
      }
   }
}
```

### Create Selectors

```bash
npm i reselect
```

```js
import { createSelector } from "reselect";

const selectData = state => state.myData;

const selectOne = createSelector(
    selectData,
    data => data.one
);

const selectTwo = createSelector(
    selectData,
    data => data.Two
);

export const selectOneTwo = createSelector(
    [selectOne, selectTwo],
    (one, two) => ({
        one, 
        two
    })
);
```

### Hooks

Reference: [Link1](https://reactgo.com/react-redux-hooks/), [Link2](https://react-redux.js.org/next/api/hooks)

#### Access Redux State

Reference: [Link](https://react-redux.js.org/next/api/hooks)

```js
import { useSelector } from 'react-redux';

const counter = useSelector(state => state.counter);
```

#### Change State using dispatch

```js
import { useDispatch } from 'react-redux';

const dispatch = useDispatch();

dispatch({ type: 'increment-counter' }); // dispatch action
```

## JWT Auth Setup

```bash
npm i jwt-decode is-empty
```

### Auth Reducer

```js
const isEmpty = require("is-empty");

const initialState = {
   isAuthenticated: false,
   user: {},
};

const authReducer = (state = initialState, action) => {
   switch (action.type) {
      case "SET_CURRENT_USER":
         return {
            ...state,
            isAuthenticated: !isEmpty(action.payload),
            user: action.payload
         };
      default:
         return state;
   }
};

export default authReducer;
```

### Errors Reducer

```js
const errorReducer = (state = {}, action) => {
    switch (action.type) {
        case "GET_ERRORS":
            return action.payload;
        default:
            return state;
    }
};

export default errorReducer;
```

### Auth Actions

```js
import setAuthToken from "../utils/setAuthToken";
import Axios from "axios";

export const registerUser = (userData, history, dispatch) => {
   console.log("userData", userData);
   Axios.post("/api/users/register", userData)
      .then(res => {
         history.push("/login");
      })
      .catch(err => {
         dispatch({
            type: "GET_ERRORS",
            payload: err.response.data,
         });
      });
};

export const loginUser = userData => {
   return {
      type: "SET_CURRENT_USER",
      payload: userData,
   };
};

export const setCurrentUser = decoded => {
   return {
      type: "SET_CURRENT_USER",
      payload: decoded,
   };
};

export const logoutUser = () => {
   // remove token from local storage
   localStorage.removeItem("jwtToken");
   // remove auth header for future requests
   setAuthToken(false);
   // set current user to empty object {} which will set isAuthenticated to false
   return setCurrentUser({});
};
```

### Set Auth Token Utility

```js
import axios from "axios";

const setAuthToken = token => {
    if (token) {
        // apply authorization token to every request if logged in
        axios.defaults.headers.common["Authorization"] = token;
    } else {
        // delete auth header
        delete axios.defaults.headers.common["Authorization"];
    }
};

export default setAuthToken;
```

### Usage In React

```jsx
const App = () => {
   if (localStorage.jwtToken) {
      const token = localStorage.jwtToken;
      setAuthToken(token);

      // decode jwt token
      const decoded = jwt_decode(token);
      const dispatch = useDispatch();

      dispatch(setCurrentUser(decoded));

      const currentTime = Date.now() / 1000;
      if (decoded.exp < currentTime) {
         dispatch(logoutUser());

         window.location.href = "/login";
      }
   }

   return (
      <Router>
         <div className="app">
            <Navbar />
            {/* {localStorage.jwtToken ? <Redirect to="/dashboard" /> : null} */}
            <Route exact path="/" component={Landing} />
            <Route exact path="/login" component={Login} />
            <Route exact path="/register" component={Register} />

            <Switch>
               <PrivateRoute exact path="/dashboard" component={Dashboard} />
            </Switch>
         </div>
      </Router>
   );
};
```

## Framer motion

### Animate childrens one by one

```js
import { motion } from "framer-motion";

// variants
const listVar = {
  hidden: {
    opacity: 0,
    y: "-100vh",
    transition: { when: "afterChildren" }
  },
  visible: {
    opacity: 1,
    y: 0,
    transition: {
      duration: 1,
      when: "beforeChildren",
      staggerChildren: 0.3 // makes children animate one by one
    }
  }
};

const itemVar = {
  hidden: { opacity: 0, x: "-100vw" },
  visible: {
    opacity: 1,
    x: 0,
    transition: { duration: 0.8 }
  }
};

// component
<motion.ul initial="hidden" animate="visible" variants={listVar}>
  <motion.li variants={itemVar}>One</motion.li>
  <motion.li variants={itemVar}>two</motion.li>
  <motion.li variants={itemVar}>three</motion.li>
</motion.ul>
```

## Formik

```bash
npm install formik --save
```

**Basic formik use**

```js
import { Formik } from "formik"

<Formik
  initialValues={{
    applesAmount: 0,
  }}
  onSubmit={values => {
    buyApples(values.applesAmount)
  }}
>
  {({ handleChange, handleBlur, values, handleSubmit }) => (
    <form onSubmit={handleSubmit}>
      <label htmlFor="applesAmount">
        <span>Amount of apples:</span>
        <input
          name="applesAmount"
          type="number"
          onChange={handleChange}
          onBlur={handleBlur}
          value={values.applesAmount}
          required
        />
      </label>
      <button type="submit">Buy apples</button>
    </form>
  )}
</Formik>
```

**References**

- formik basics: [link](https://atomizedobjects.com/blog/react/how-to-use-formik-in-react/)
- formik, yup, material ui: [link](https://maneesha-erandi.medium.com/react-form-validation-with-formik-material-ui-and-yup-1cd92eac887)

## Helper Functions

### Truncate string

```js
export function truncate(str, limit) {
    if (typeof str !== "string") return;
    if (str.length > limit) {
        const endIndex = limit - 3;
        let newStr = str.slice(0, endIndex) + " ... ";
        return newStr;
    } else {
        return str + " ";
    }
}
```

### Capitalize string

```js
export function capitalize(str) {
    return str.charAt(0).toUpperCase() + str.slice(1);
}
```

### Open Link

```js
export function openLink(url) {
    window.open(url, "_blank");
}
```
