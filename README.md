# global.env

ECMAScript proposal, specs, and reference implementation for `global.env`, a standard representation of the current build environment.

Spec drafted by @milesj and @kesne.

Requires the [global proposal](https://github.com/tc39/proposal-global) by @ljharb.

## Rationale

There's no standardized, cross-platform, developer-friendly way to detect what environment you're running code in. The JavaScript community has generally centered around using `process.env.NODE_ENV`, but this property feels out of place in the browser, within mobile, and any other non-Node.js environment.

Furthermore, consistently typing `process.env.NODE_ENV !== 'production'` can be rather verbose, and using `__DEV__` flags requires a transpilation step, this proposal aims to provide a concise and better developer experience.

### Why `global`?

Using the `global` object allows for instant access and evaluation from within any scope, and once the [global proposal](https://github.com/tc39/proposal-global) lands, this object will further allow for cross-platform consistency and interoperability.

### Why `global.env`?

The `global.env` property is currently undefined across all platforms. Please refer to the [Naming](#naming) chapter for more information.

## Implementation

TODO

## Naming

We chose the name `env`, as it's short, and most programming languages, build tools, and developers are familiar with this term -- e.g., `.env` files, `ENV` variables, etc. Additionally, `window.env` is available in the browser, and `global.env` in Node.js (and ReactNative).

### Availability

| Platform | `global` defined? | `global.env` available? | Current `env` detection |
| --- | --- | --- | --- |
| Browsers | Under `window` | Yes | |
| Node.js | Yes | Yes | `process.env.NODE_ENV` |
| Electron (uses Node.js) | ~ | ~ | ~ |
| JavaScriptCore | Under `window` | Yes | |
| ReactNative (uses JSC) | ~ | ~ | `__DEV__` |
| V8 | Under `window` | Yes | |

> The tilde (~) inherits from the parent.
