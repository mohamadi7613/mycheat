

## jsconfig

. The presence of jsconfig.json file in a directory indicates that the directory is the root of a JavaScript Project. 
. jsconfig.json is a descendant of tsconfig.json,


```json

{
    "compilerOptions": {     // import MyComponent from "components/MyComponent";
        "baseUrl": "./src",
        "checkJs": false,
        "jsx": "react"
    }
}
```
