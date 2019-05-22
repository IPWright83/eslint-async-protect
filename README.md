# `eslint-plugin-async-protect`
This ESLint plugin enforces the following:

 - functions defined with the `async` keyword should have an `Async` suffix on the name
 - calls to functions with an `Async` suffix on the name should be called with `await`
 
## Installation
Assuming that you already have ESLint installed, simply run:

```
npm install --save-dev eslint-plugin-async-protect
```

## Configuration
Within your `.eslintrc` file you need to include the plugin, and specify the level the rules should apply:
```
{
   "plugins": [
      "async-protect"
   ],
   "rules": {
       "async-protect/async-suffix": "error",
       "async-protect/async-await": "warn",
   }
}
```
The general recommendation is to use `warn` for the `async-await` rule. This is because for 3rd party code you do not have control over function names, and the plugin may complain at extraneous `await` keywords.

## Rules

### async-suffix
This rule enforces that `async` functions have an `Async` suffix to their name. Functions not defined as `async` should not have the suffix. Here are some examples:

##### valid
```
const fooAsync = async function() {}
async function fooAsync() {}
class Bar {
   async fooAsync() {}
   foo() {}
}

function foo() {}
const foo = function() {}
```

##### invalid
```
const foo = async function() {}
async function foo() {}
class Bar {
    async foo() {} // invalid
    fooAsync() {}  // invalid
}

function fooAsync() {}
const fooAsync = function() {}
```

### async-await
This rule enforces that all functions with an `Async` suffix on their name should be called with an `await`. Functions without the suffix should not be called with an `await`.

There are times when you may wish for an `async` function to run in the background, or where you need to call 3rd party code that doesn't follow the `async-suffix` naming convention. In that case use `// eslint-disable-line async-protect/async-await` to disable the rule for that line.

##### valid
```
await fooAsync();
const result = await fooAsync();
const result = (await fooAsync()).result;
const result = await foo().bar().bazAsync();

const result = foo();
```

##### invalid
```
await fooAsync();
const result = fooAsync();
const result = fooAsync().result;
const result = foo().bar().bazAsync();

const result = await foo();
```
