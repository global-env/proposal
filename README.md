# global.env

Proposal, specs, and reference implementation for `global.env`, a standard representation of the current runtime environment.

Spec drafted by [@milesj](https://github.com/milesj) and [@kesne](https://github.com/kesne).

Requires the [global proposal](https://github.com/tc39/proposal-global) by [@ljharb](https://github.com/ljharb).

## Rationale

There's no standardized, cross-platform, developer-friendly way to detect what environment you're running code in. The JavaScript community has generally centered around using `process.env.NODE_ENV`, but this statement feels out of place in the browser, within mobile, and any other non-Node.js environment.

Furthermore, consistently typing `process.env.NODE_ENV !== 'production'` is rather verbose, and using `__DEV__` flags require a transpilation step. This proposal aims to provide a straight-forward and greater developer experience.

#### Why `global`?

Using the `global` object allows for instant access and evaluation within any scope, and once the [global proposal](https://github.com/tc39/proposal-global) lands, this object will further allow for cross-platform consistency and interoperability.

#### Why `global.env`?

The `global.env` property is currently undefined across all platforms. Please refer to the [Naming](#naming) chapter for more information.

## Implementation

The `global.env` property is an object that provides the current environment as a constant flag, an `is()` method for comparison, and additional boolean shorthand properties for easy lookup.

```js
Object.defineProperty(global, 'env', {
  configurable: false,
  enumerable: false,
  writable: false,
  value: Object.freeze({
    CURRENT: ENV_FLAG,
    dev: (ENV_FLAG === 'development'),
    prod: (ENV_FLAG === 'production'),
    is(name) {
      return (ENV_FLAG === name);
    },
  }),
});
```

> `ENV_FLAG` will resolve to `"production"` in a browser, and `process.env.NODE_ENV` in Node.js.

All properties on the `global.env` object are immutable and *cannot* be modified at runtime.

#### `global.env.CURRENT`

The current environment as a string. Defaults to `"production"`.

#### `global.env.dev`

A boolean flag for detecting the `development` environment. A shorthand for `global.env.is('development')`.

#### `global.env.prod`

A boolean flag for detecting the `production` environment. A shorthand for `global.env.is('production')`.

#### `global.env.is()`

A function for comparing an argument against the current environment name. Can be used for situations where the environment name is not `development` or `production`.

```js
global.env.is('staging');
```

## Usage

Since the `global` object is, well, global, we can omit the `global` object path and simply use `env`. Below are a few examples of real world usage.

```js
if (env.prod) {
  // In production
}

if (env.is('test')) {
  // Unit testing
}
```

We can also replace verbose `process.env` Node.js conditionals and app-specific `__DEV__` constants.

```js
// Before
if (__DEV__) {}
if (process.env.NODE_ENV !== 'production') {}
if (process.env.NODE_ENV === 'development') {}

// After
if (!env.prod) {}
if (!env.prod) {}
if (env.dev) {}
```

## Naming

We chose the name `env`, as it's short, and most programming languages, build tools, and developers are familiar with this term -- e.g., `.env` files, `ENV` variables, etc. Additionally, `window.env` is available in the browser, and `global.env` in Node.js (and ReactNative).

#### Availability

| Platform | `global` defined? | `global.env` available? | Current `env` detection |
| --- | --- | --- | --- |
| Node.js | Yes | Yes | `process.env.NODE_ENV` |
| Electron (uses Node.js) | ~ | ~ | ~ |
| JavaScriptCore (Safari) | With `window` | Yes | |
| ReactNative (uses JSC) | ~ | ~ | `__DEV__` |
| V8 (Chrome) | With `window` | Yes | |
| SpiderMonkey (Firefox) | With `window` | Yes | |
| ChakraCore (IE/Edge) | With `window` | Yes | |

> The tilde (~) inherits from the parent.

## TODO

* How to set `global.env.CURRENT` in the browser? Should this be mutable?
* If mutable, how do we restrict modification to the current application? And not from third-party modules?
