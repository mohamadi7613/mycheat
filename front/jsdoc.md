
# JSDoc

+ JSDoc is a markup language used to document JavaScript code.
+ It helps generate API documentation and provides better code intelligence in IDEs.
+ Similarly Python uses docstrings for documentation
+ Vscode has a built-in support for JSDoc

```js
/**
 * Basic syntax
 * Description 
 * @tagName {type} name - Description
 */
```

```bash
npm install -g jsdoc            # install jsdoc
jsdoc src -r -d docs             # generate document
```

### 1. Functions

```js
/**
 * Adds two numbers
 * @param {number} a - First number
 * @param {number} b - Second number
 * @returns {number} Sum of a and b
 */
function add(a, b) {
  return a + b;
```

















