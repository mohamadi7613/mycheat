


https://github.com/uanders/react-redux-cheatsheet
https://reactcheatsheet.com/
https://github.com/vincsb/react-cheat-sheet
https://github.com/typescript-cheatsheets/react?tab=readme-ov-file

```
npm install --save prop-types  // runtime type checking for React props and similar objects
```

// notes: don't forget the command lines


# 0. Conecpts

## File structure
src/
|-- api/                         # Axios configuration and API utility
|   |-- axiosClient.js           # Axios instance with default settings
|   |-- endpoints.js             # API endpoint constants (optional)
|-- store/                       # App-level configuration
|   |-- store.js                 # Configures Redux store
|   |-- rootReducer.js           # Combines feature reducers
|-- features/                    # Feature-specific slices and logic
|   |-- feature1/                # Like products    
|   |   |-- feature1Slice.js     # Reducers
|   |   |-- feature1Actions.js   # async logic
|   |-- feature2/                # Like Users
|       |-- feature2Slice.js
|       |-- feature2Actions.js
|-- components/                  # Reusable components
|   |-- ExampleComponent.jsx
|-- containers/                  # components benefits redux
|-- pages/                       # Page-level components
|   |-- Feature1Page.jsx
|   |-- Feature2Page.jsx
|-- hooks/                       # custome hooks
|-- hoc/                         # HOC
|-- layout/                      # basic structure of App like Header, footer and Sidebar
|-- styles/                      # CSS or SCSS files
|-- utils/                       # Helper functions
|-- assets/                      # assets
|-- __tests__/                   # writing tests
|-- index.js                     # Application entry point
|-- App.js                       # Root component



## virtual dom
The Virtual DOM is a lightweight JavaScript object that mirrors the structure of the real DOM.
Instead of manipulating the actual DOM directly, React creates a virtual representation of it in memory.
When the state or props of a React component change, React updates the Virtual DOM first. After finding the differences, React updates only the parts of the real DOM that changed.

This Virtual DOM works in three simple steps-
 i. Whenever any underlying data changes, the entire Ul is
    re-rendered in Virtual DOM representation
 ii. Then the difference between the previous DOM representation 
      and the new one is calculated
 iii. Once the calculations are done, the real DOM will be updated with only 
       the things that have actually changed
+ Virtual dom is not faster the real DOM bcz in the background it uses real DOM, it is just smarter.

```js
import ReactDOM from 'react-dom/client';        // imp virtual dom from liberay
const root = document.getElementById('root');   // get this id from index.html file
const reactRoot = ReactDOM.createRoot(root);   // create a virtual dom and pass the real dom
reactRoot.render (<App/>)   //pass first component
// render can not render [obj, bool, null, undefined] but can render [jsx, number, array, fragment]
```

## env 
. it is a text file for storing personal info like keys
. we should use `REACT_APP` as a prefix for any keys
. we can access it in our js file by `process.env.REACT_APP_xxx`
. any update in .env file requires restart whole app
. Vercel for deploying our app have a feature to fill our .env file, so there is no need to push to github

## Strict mode 
. run useEffect twice instead of once, you can check it with and empty array
. its only available in developing mode and it will ignore in production mode 
. it give some warnings and additional checks
. you should add rules about .env file in Readme file


# 2. Syntax

## 2. syntax: conditional rendering
```js
{ isGoal ? <MadeGoal/> : <MissedGoal/> }   //Ternary Operator
{cars.length > 0 && <h2>a text</h2> }      // &&
// useful for sending props
```
##  2. syntax:jsx

```js
const submitButton = (  // we can save jsx into a variable, make it easy to read
  <>
    <div>this is a button</div>
  </>
)
```

## 2. syntax:Props

```js
<Person name="ali" age=45 />                // sends date to Person with props
<Person name={"ali"} age={45} />                // you can use {} for sending props
<Person> <p>this is a p tag</p> </Person>    // sends tags to Person with props.children
// props.name = "hamid"  //Error bcz props are read only.
//  css do not appy on custom components with className [they send with props]
// listeners like onClick do not applt on custom components [they send with props]
```

## 2. syntax:States
+ Every time state or props changes, the function of component will re-render(runs again)
+ setState in an async function so you can not see the updated value with console.log immediatly after that. but you can see updated value using console.log in the body of function (bcz re-rendering).
```js
const [id, setId] =useState(12)   // its better to use const, so "a = 13" will get error
```

## 2. syntax:CSS
```js
<div style={{backgroundColor: "red"}}> </div>   // we should use obj but in html we use string
<div className={style.header + " " + style.center}> ok </div>          // for preventing conflict between css classes we use css modules (style.module.css) 
```

## 2. syntax:Loops
```js
<div> ["this","is"] </div>    // react can display arrays ==> thisis
<div> [<p> p tag </p>, <h1 key="k"> h1 tag </h1>] </div> //  we get a warning that every element should have a key
// its better to write constant info like arrays in separate files, bcz when component re-render it will re-calculated and its not good
[1,2,3].map(item,index)=>{return(<div>{item}</div>)}
```

### 2. syntax:Form
```js
function submit(e) {       // onSubmit handler for <form> tag
      e.preventDefault()   // prevent from refresh
      let f = new FormData(e.currentTarget)  // access form values
      console.log(e.currentTarget)            // print whole <form> tag
      console.log(e.currentTarget.age)           // age comes from 'for' attribute in <input> tag
      f.forEach((item, index)={ return obj})  // its better to create an obj for values by iterating
}
```

### 2. syntax:Fetch
```js
const BASE_URL = "www.google.com" //config.js
const API_KEY_PARAM = "?api_key=123"
export class StudnetAPI {  // we can access to each class by StudnetAPI.allStudnets()
  static async allStudnets(){  // Static methods can be used without creating instance
    const response = await axios.get(`${BASE_URL}/table/${API_KEY_PARAM}`)
    return response.data
  }
}
useEffect(()=>{
  async function(){StudnetAPI.allStudnets()} // we need to define the outer function as an asnyc
},[]) // empty array is good for fetching data before render
```

# 3. Hooks

### 3. Hooks: useEffect
```js
// why we need? bcz if we save the result of axios in setSate it will re-render [infinit loop]  
// useEffect is a listener when compoent starts and remove
useEffect(()=>{}, [])    // 1. function 2. array[prop, state]
useEffect(()=>{return ()=>{} }, []) // return will run once when the component remove from the dom
// React is smart and when a component remove from a dom it do not keep variables in memory, but when we have something like setInterval for every second we will need to remove it by retrun in useEffect
// Also return function run in every rerender when we have Timeouts, subscriptions, event listeners
function Effect() {
  const [a, setA] =useState(45)
  useEffect(()=>{
    const s= setInterval(() => {
      console.log("iiiiii")   // this print should log every 3 second
    }, 3000); // if you click multiple times you can this print much earlier
    // return (()=>clearInterval(s))    // add this line to as a Cleanup method
  },[a]) // we would have a new interval in every new click, so with 50 click we will have 50 intervals
  return (
      <div onClick={()=>{setA(Math.random)}}>click me</div> // when click states change and func rerender
  )
}
```

### 3. Hooks:State batching
State batching introduced in React18 and it is an optimization where multiple state updates are grouped together into a single render, improving performance by reducing the number of renders triggered.
```js
const async handleClick = () => {
  await axios.get("url.json")  // before react18 we saw multiple "hi" in the console
  setCount(count + 1); 
  setFlag(!flag);      // but in react 18 all these updates will be batched
  console.log("Hi");  
};
```

###  3. Hooks: useRef
+ provides a way to persist values across renders without causing re-rendering of the component.
+ It returns a mutable object with just one property `current`
+ useRef is great for accessing DOM elements 
```js
import React, { useRef } from 'react';
const MyComponent = () => {
  const inputRef = useRef(null);   // intial value
  const handleClick = () => {
    console.log(inputRef)    // {current: input} holds the input with ref prop
    inputRef.current.focus(); // famouse exmaple is focus on the input
  };
  return (
    <div>
      <input ref={inputRef} /> // we should use ref props for accessing the DOM
      <button onClick={handleClick}>Focus on Input</button>
      {ref.current}   // will not change since it cause re-rendering
    </div>
  );
};
```

###  3. Hooks: useMemo
+ cache the result of a calculation between re-renders
```js
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]); // this code runs the function
// () => is Required bcz it means lazy Evaluation and this function not called immediately
import {memo} from 'react'  // memoise a component and not rerender unitl props changes
function App_(){return <div>send</div>}
export const App = memo(App_)
export const App = memo(()=>{return <div>send</div>}) // anotehr appraoch
```

### 3. Hooks: useCallback
useCallback is a React Hook that lets you cache a function definition between re-renders.
```js
function App(){
  const cachedFn = useCallback(()=>{console.log("Goodbuy"),[]}) // there is no dependencies for this
  // if we memoise the component, its better to memoise the functions using useCallback
  // in every render of App all the functions including props functions, and if inner components have functions in their props it causes rerendering
  return(
    <div>
      <Hello onClick={()=>{console.log("hello")}} />      // not memoised
      <Goodbuy onClick={cachedFn} />                    // memoised
    </div>
  )
}
```


###  3. Hooks: useReducer
alternative to useState for managing more complex state logic
```js
export default function Counter() {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 }); //(reducer, initialValue)
  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>Increment</button>
    </div>
  );
}
```


##  3. Hooks: Context

```js
////context.js
export const ThemeContext = createContext({name: "my theme"})
///App.js
import {createContext, useContext} from 'react'
export function App(){
  const myTheme= useContext(ThemeContext)
  const [theme,setTheme]= useState(myTheme)
  return( // like redux we need a provider
    <ThemeContext.Provider value={theme,setTheme}> //pass the state to provider
        <App/>
    </ThemeContext.Provider> // provider and consumer are attribure
  ) // for each kind of data we need a provider tag to wrap, so its better to use redux when we have a lot data. ANd if we use one tag and send all diff data, performance will be reduce. for x, if we update a pieace of date in the context, all the consumers that uses the global context will re-render
}
// we can access to the context with two method
///component.js
export function Component(){ // first method is by a function(not recomended)
  return(
    <ThemeContext.Consumer value={theme,setTheme}> // this should be value
        {(value)=>{  // we access the context within a fucntion
          return(<div>{value}</div>)
        }}
    </ThemeContext.Consumer>
  )
}
///component.js
export function Component(){ // second method is by using useContext
  const {theme,setTheme} = useContext(ThemeContext)
  return(<div>{theme}</div>)
}
```

## 3. Hooks: Custome Hooks
+ Generally hooks are related to re-render the component.
+ Hooks are just simple regular functions which get regular arguments(not props)
+ For x, we can not pass useEffect to a component but we can create a custome hook for this
```js
function useWindowWidth() {  // its a convention that starts the name with use
  const [width, setWidth] = useState(window.innerWidth); //a commmon example is using usestate and useEffect 
  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth);
    window.addEventListener('resize', handleResize);  // listener is good example
    return () => window.removeEventListener('resize', handleResize);
  }, []);
  return width; // just return state
}
function Component() {
  const width = useWindowWidth();
  return <p>Window width: {width}px</p>;
}
```

# HOC (Higehr Order Component)
+ A function that returns another function is typically referred to as a higher-order function. it can be described as a factory function
+ HOC is a function that takes a component as an argument and returns a new enhanced component.
+ HOCs are pure functions, meaning they don’t modify the original component. Instead, they return a new component with added functionality
+ example : protecting routes with sigin
```js
function withAuthRequired(component){ // by convention starts with "with"
  return function ProtectedComponent(){ //this is a factory that returns a function
    if(!user){ navigate("/signin")} // get user from redux or localstorage
    else{return component}
  }
}
```

# 4. React Lib:

## 4. React Lib:React-Router

```js

```

## 4. React Lib:React-styled component
1. just critical css
2. no class name bug
3. easier deletaion (when delete c component in react)
4. Dynamic styling

File structure
1. /Components
1.1 Button.js   [import and export in this file]
1.2 Button.style.js
1.3 App.js    [import from Button.js]

```js
import styled from 'styled-components'
export const styledComponent1 = styled.button`color: red;` 
export const styledComponent2 = styled(styledComponent1)`border-color: red;`  //extends inheritance
function App(){
  return(<div><styledComponent> your button </styledComponent></div>)
}
```


### 4. React Lib:React query
+ Caching: Memoizing and show data on the screne and you do not see the loading then send a request to the API then make comparsion and if there was a difference rerender the page
+ multiple requests (if req fail)
```js
import { QueryClient, QueryClientProvider, useQuery } from 'react-query'
const queryClient = new QueryClient() // {queries:{retry: 10}} repeat 10 times if any req fails. so we can config queries with this object
export default function App() {
  return ( // like redux we need to wrap the root component
    <QueryClientProvider client={queryClient}>
      <Example />
      <ReactQueryDevtools initialIsOpen={false} />  // at the bottom adds a button for developers
    </QueryClientProvider>
  )
}
function Example() {
  const { isLoading, error, data } = useQuery( // get three states of fetching from hook 
    'repoData',   // query key should be unique for caching. // it can be array also
    () => fetch('api.json').then(res =>res.json()), // two argument is mandaory: 1.key 2. function
    {initialData:[1,2,3],staleTime: 1000,}   // staleTime: wait this time and then send a req to fetch the cached data again
  ) // useQuery will trigger ony at the first render so we do not need useEffect with an empty array
  return (
    <div>
      <div>{data}</div>
      {isLoading && 'Loading...'}
      {error&& error.message}
    </div>
  )
}
```

# 5. class components


