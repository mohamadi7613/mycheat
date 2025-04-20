# Redux
. why we need redux? passing props in compilcated dom



# File structer for Redux pure
1. Redux
1.1 actions
1.2 reducer
1.3 store
1.4 type
4.5 middlewares
2. components




## Redux-toolkit
1. Reduce out boilerplate code like switch-case, action-creator, action object
2. Third packages like Redux-devtools, Immer and Redux-thunk is not required
. by convention the components that interact with redux should be located in `containers` folder next to components

### 1. Store
. Store holds the state of the App that stores in a single obj
```js
// store.js
import { configureStore } from '@reduxjs/toolkit'   // in standard Redux usage pattern we need redux toolkit
// This creates a Redux store, and also automatically configure the Redux DevTools extension
export default configureStore({
  reducer: {  // The store is created by passing in a reducer
        counter: counterSlice, // this is our reducer from createSlice
  },    
})
```

##### 1.1 store methods
```js
import store from './app/store.js'  
store.getState()    // access to the current global state
store.dispatch(action)   // change the state
store.subscribe(listener)   // listener is a function that will execute whenever satet changes
const subscribe = store.subscribe(() =>
    console.log('State after dispatch: ', store.getState()) //Every time the state changes, log it
    // Note that subscribe() returns a function for unregistering the listener
)
store.dispatch({ type: 'todos/todoAdded', payload: [age : 15]}) // we can pass an action directly or an actopn creator
```

### 2. Provider

```js
import store from './app/store.js'            
import { Provider } from 'react-redux'
const root = ReactDOM.createRoot(document.getElementById('root'))
root.render(
  <Provider store={store}>   // pass the store as a prop
    <App />
  </Provider>,
)
```


## 3. Reducer
. Reducer is a pure function that recives prevous store and return new store
. Reducer is like a listner that recieves an action and do something
. reducer as a function recievs two argument : 1. pre state 2. action which is an obj {type, payload}
. reducer should pass to the store, bcz it is responsible for updating the store
. reducers are not allowed to modify the existing state. Instead, they must make immutable updates, by copying the existing state and making changes to the copied values.
. reducers must not do any asynchronous logic, calculate random values, or cause other "side effects"

```js
const reducer = (state, action) =>{
  switch(acton.type){
    case STUDENT_LIST :
      return {...state, data= action.payload} //make immutable updates with spread operator
      //state.value = 123   // this is illegal bcz mutate the state in the reducer
    default:
      return preState
  }
}
```

## Type
The "type" field is a string that gives actions a descriptive name, like "todos/todoAdded".
We usually write that type string like "domain/eventName",
where the first part is the feature or category that this action belongs to, and the second part is the specific thing that happened.

### Action
An action is a JS object that has with a "type" property and "payload" property
Action creator is a function that returns an action

```js
const STUDENT_LIST = "STUDENT_LIST"
function get_student_list(){  // this function called "action creator"
  return {type: STUDENT_LIST , payload: [1,2,3] } // return action
}
```
we can also use bindActionCreators but it is not neccessery
```js
import bindActionCreators from 'redux'
const actions = bindActionCreators ({get_student_list,action_creator },store.dispatch)
action.get_student_list() // for better reading
```



### 4. Dispatcher
. State is Read-Only and the only way to update the state is to emit an action by calling store.dispatch() and pass in an action object. we are not allowed to directly update the state object.

```js
import { useDispatch } from 'react-redux';
const dispatch = useDispatch()
dispatch(editCompany)
const editCompany = (values) => {
    return (dispatch) => {
        dispatch({ type: "students/loading", payload: {} })
        axiosInstance.post("/edit-company", {
            values
        }).then((response) => {
            dispatch({ type: "students/success", payload: response.data.data })
        }).catch((e) => {
            dispatch({ type: "students/error", payload: {}, error: e.response.data })
        })
    }
}
```




## Slice
The global State splits into diffrent slices
```js
import { createSlice } from '@reduxjs/toolkit'
export const studnetsSlice = createSlice({ // two argument
  name: 'students',   // the first part of action type. The second part is the name of function in reducers
  initialState: { // we also access to this by store.students in components
    data: 0,
    loading : false,
    error: false
  },
  reducers: { // there is no need to switch-case like pure redux
    success: (state, action) => { // success is a key like FETCH_USERS_SUCCESSED in pure redux
        value = action.payload   // we can directly mutate the state unlike pure redux
        loading = false         // we do not to return anything like pure redux
    },
    error: (state, action) => {
        error = action.payload
        loading = false
    }
  }
})
export const { success, error } = studnetsSlice.actions // export actions so you can access these functions
export default studnetsSlice.reducer  // export this as a default for passing the store
```

## Extra reducers
In the pure redux this can be done giving another case into our switch(type comes from other reducer)
```js
import cakseActions from "./cakeSlise"
export const studnetsSlice = createSlice({
  name: 'icecream',
  initialState: {
    icecreamNumber: 10,
  },
  reducers: {     
    buyIcecream: (state, action) => { 
        icecreamNumber -= action.payload   
    }
  },
  extraReducers: { // we can give actions and access to other reducers
      ["cake/buyCake"]: (state, action) =>{ // this action comes from cake reducer
        state.cakeNumber -=action.payload   // by this action two reducers will work on their states
      }
  }
  // other appraoch is using builder function
  extraReducers: (builder)=>{
    builder.addCase(cakeActions.buyCake, (state, action)=>{state.cakeNumber-=action.payload})
  }
})
```

## Immer
Immer is a liberary that simplifies handling immutable data structures
Redux-toolkit use Immer in ther background


## Redux-tooklit AsyncThunk

```js
import { createAsyncThunk, createSlice } from '@reduxjs/toolkit'

export const fetchUsers = createAsyncThunk( // we should create this outside the slice
  'users/fetchUsers',      // name comes from the slice
  ()=>{ //  this is a standard action creator
    return axios.get("www.api.com/users").then((res)=>res.data) //axios returns a promise so we use .then()
  } // there is no need to catch the error
)

const usersSlice = createSlice({
  name: 'users',
  initialState,
  reducers: {}, // using for sync actions
  extraReducers: (builder) => {  // using for async actions
    builder.addCase(fetchUsers.pending, (state, action) => { // outside the slice we create an action "users/fetchUsers"    
      state.loading =true
    })
    builder.addCase(fetchUsers.fulfilled, (state, action) => { // addCase is a method
      state.loading= false
      state.data=action.payload
      state.error= ''
    })
    builder.addCase(fetchUsers.rejected, (state, action) => { // first arg is the name of action
      state.loading= false
      state.data=[]
      state.error= action.payload.message
    })
  },
})
export userSlice.reducer
```

### Selectors
Selectors are functions that know how to reac specific pieces of information from a store state value.
```js
import { useSelector } from 'react-redux';
const students = useSelector(store => store.students)   //const bcz state is read-only
``` 


## Async best practise in pure redux
```js
const initialState = {
  loading: false,
  data: [],
  current: {},  // for a single data
  error: ""
}
const FETCH_USERS_REQUEST = "FETCH_USERS_REQUEST"   // loading: true
const FETCH_USERS_SUCCESSED = "FETCH_USERS_SUCCESSED"  // loading: false
const FETCH_USERS_FAILED = "FETCH_USERS_FAILED"      // loading: false
//Each reducer manages all operations (GET, ADD, EDIT, DELETE) for each role.
```

## Middleware
Middleware sits between the action dispatching and the reducer, giving you the chance to act on the action before it hits the reducer. Popular Middleware: 1.Redux-Logger 2.Redux-DevTools 3.Redux-Saga 4.Redux-Persist
Miffleware is a way to extend redux functionality like logging and async tasks.

### Redux-logger
Every change in state will be logged automatically in the console
```js
import logger from 'redux-logger'
const store = createStore(
  reducer,
  applyMiddleware(logger)
)
////////redux-toolkit
import { configureStore } from '@reduxjs/toolkit'
export default configureStore({
  reducer: { counter: counterSlice},   
  middleware : (getDefaultMiddleware) =>getDefaultMiddleware().concat(logger)  // we should concat bcz by default there are some middlewares by RTK
})
```


### Redux-thunk
Redux Thunk allows you to write action creators that return a function instead of an action. This is useful for handling asynchronous logic (e.g., fetching data from an API).If you're using Redux Toolkit package as recommended, there's no need to redux-thunk as configureStore API already adds the thunk middleware by default.

```js
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers';

const store = createStore(
  rootReducer,
  applyMiddleware(thunk) // Applying Redux Thunk
);
```

```js
const fetchUserData = () => { // return a function instead of an object
  return (dispatch) => { // redux-thunk provide dispatch as an argument in this function
    dispatch({ type: 'FETCH_USER_REQUEST' });
    setTimeout(() => {  // Simulate an API call 
      dispatch({ type: 'FETCH_USER_SUCCESS', payload: { name: 'John Doe' } }); // Writing an Asynchronous Action:
    }, 2000);
  };
};
store.dispatch(fetchUserData()); // Dispatch the thunk action
```

### Redux-persist
if you refresh redux will loos the data in th UI

```js
import { persistStore, persistReducer } from 'redux-persist'
import storage from 'redux-persist/lib/storage' // defaults to localStorage for web
import { combineReducers, configureStore } from '@reduxjs/toolkit'
import { FLUSH, REHYDRATE, PAUSE, PERSIST, PURGE, REGISTER} from 'redux-persist'

const rootReducer= combineReducers({  // combine the reducers into a single reducer
  studnet : studnetsSlice.reducer // pass all reducers from slices
}) // its better to write all reducers into another file called "reducers"

const persistConfig = {
  key: 'root',  // set a uniqe key bcz you can have multiple stores
  storage,       // import storage from  redux-persist and tell redux to use the local storage bcz there are lots of other storage engines like session
  blacklist: ['navigation'] // navigation will not be persisted
  whitelist: ['navigation'], // only navigation will be persisted

}
const persistedReducer = persistReducer(persistConfig, rootReducer)  // persist the reducers with this config
const store = configureStore({
  reducer : persistedReducer,
  middleware: (getDefaultMiddleware) => // add a middlewareto ignore all the actions send by redux-persist
  getDefaultMiddleware({
    serializableCheck: {
      ignoredActions: [FLUSH, REHYDRATE, PAUSE, PERSIST, PURGE, REGISTER],
    },
  }),
})  // or createStore(persistedReducer) in older version
const persistor = persistStore(store) // create a persisted version of the store
export {store,persistor }  // export both
```

```js
// index.js
import { PersistGate } from 'redux-persist/integration/react'
const App = () => {
  return (
    <Provider store={store}>  // we export both store and persistor
      <PersistGate persistor={persistor}>
        <RootComponent />
      </PersistGate>
    </Provider>
  );
};
```
