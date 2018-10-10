# Mopidy.js

[![Latest npm version](https://img.shields.io/npm/v/mopidy.svg?style=flat)](https://www.npmjs.org/package/mopidy)
[![Number of npm downloads](https://img.shields.io/npm/dm/mopidy.svg?style=flat)](https://www.npmjs.org/package/mopidy)
[![Build Status](https://img.shields.io/travis/mopidy/mopidy.js.svg?style=flat)](https://travis-ci.org/mopidy/mopidy.js)

Mopidy.js is a JavaScript library for controlling a Mopidy music server over a
WebSocket from the browser or from Node.js.

The library makes Mopidy's core API available from the browser and Node.js
programs, using JSON-RPC messages over a WebSocket to communicate with Mopidy.

This is the foundation of Mopidy web clients.

## Getting it for browser use

A minified versions of Mopidy.js, complete with sourcemap, is available from
the project's
[GitHub release page](https://github.com/mopidy/mopidy.js/releases).

## Getting it for Node.js use

If you want to use Mopidy.js from Node.js instead of a browser, you can install
Mopidy.js using Yarn:

```
yarn add mopidy
```

Or using npm:

```
npm install mopidy
```

After installing, you can import Mopidy.js into your code using `require()`:

```js
const Mopidy = require("mopidy");
```

Or using ES6 imports:

```js
import Mopidy from "mopidy";
```

## Using the library

See the [Mopidy.js documentation](https://docs.mopidy.com/en/latest/api/js/).

## Building from source

Install [Node.js](https://nodejs.org/) and [Yarn](https://yarnpkg.com/).

Enter the source directory, and install all dependencies:

```
yarn
```

That's it.

You can now run the tests and linters:

```
yarn test
```

To build updated JavaScript files for browser use in `dist/`, run:

```
yarn build
```

## Changelog

### 1.0.0 (UNRELEASED)

- **Backwards incompatible:** The `Mopidy` class can no longer be instantiated
  without the `new` keyword.

  To upgrade existing code:

  ```js
  // Change from this:
  const mopidy = Mopidy(...);
  // To this:
  const Mopidy = new Mopidy(...);
  ```

- **Backwards incompatible:** The API methods no longer support two different
  calling conventions.

  To upgrade code using `by-position-or-by-name`, simply remove
  `callingConvention` from the settings object:

  ```js
  // Change from this:
  const mopidy = new Mopidy({ callingConvention: "by-position-or-by-name" });
  // To this:
  const mopidy = new Mopidy();
  ```

  To upgrade code using the long deprecated, but still default, calling
  convention `by-position-only`, multiple steps is required.

  1.  The first step is to remove `callingConvention` from the settings object:

      ```js
      // Change from this:
      const mopidy = new Mopidy({ callingConvention: "by-position-only" });
      // To this:
      const mopidy = new Mopidy();
      ```

  2.  The second step is to update all API method calls to explicitly use
      positional arguments:

      ```js
      // Change from this:
      mopidy.tracklist.setRepeat(true);
      // To this:
      mopidy.tracklist.setRepeat([true]);
      ```

      At this point, the application should work again.

  3.  The final, and optional step, is to change API method calls to using objects
      instead of arrays where that makes the code clearer:

      ```js
      // Optionally change from this:
      mopidy.library.search(["abba", null, true]);
      // To this:
      mopidy.library.search({ query: "abba", exact: true });
      ```

- **Backwards incompatible:** The `Mopidy` class no longer reexports When.js
  as `Mopidy.when()`. To upgrade existing code, either migrate to standard
  ES6 `Promise` or add When.js as a dependency to your project.

- **Backwards incompatible:** The event listening API has changed a bit as
  Mopidy.js has switched from the unmaintained BANE implementation to Node.js'
  [`EventEmitter` API](https://nodejs.org/api/events.html).

  The following methods has been removed:

  - `mopidy.on(listener)` --
    Subscribe to more specific events using `mopidy.on(event, listener)`.
  - `mopidy.off(listener)` --
    Unsubscribe from specific events using `mopidy.off(event, listener)`.
  - `mopidy.off(event)` --
    Unsubscribe specific listeners using `mopidy.off(event, listener)`.
  - `mopidy.bind(...)`
  - `mopidy.errback(listener)`

  The following methods works as before:

  - `mopidy.on(event, listener)`
  - `mopidy.once(event, listener)`
  - `mopidy.off(event, listener)` -- Note that Node.js only added
    this method in Node.js 10.0.0. Consider upgrading Node.js or replacing
    `mopidy.off()` with `mopidy.removeListener()`.
  - `mopidy.emit(event, ...)`

- For exploring what events Mopidy.js emits, two new aggregate event types
  has been added:

  - `state`: This event emits online/offline/reconnection events,
    like `state:online`.
  - `event`: This event emits server side events from Mopidy,
    like `event:trackPlaybackStarted`.

  Listeners to the aggregate events get arguments on the form
  `(eventName, data)`, where `eventName` is the name of the more specific
  event. It is recommended that applications do not use these aggregates, but
  instead subscribe to more specific events.

- Modernized dependencies:

  - The `Promise` object standardized in ES6 has replaced When.js.
  - The `EventEmitter` object from Node.js, with polyfills for the browser,
    has replaced BANE.
  - `isomorphic-ws` and `ws` has replaced our own wrapper around the browser's
    `WebSocket` API and `faye-websocket` on Node.

- Modernized development stack:

  - Testing: Jest has replaced Buster.JS and Sinon.
  - Linting: ESLint has replaced JSHint.
  - Building: Parcel has replaced Browserify and Uglify.
  - Tasks: npm scripts has replaced Grunt.
  - Formatting: Prettier has replaced manual formatting.

### 0.5.0 (2015-01-31)

- Reexport When.js library as `Mopidy.when`, to make it easily available to
  users of Mopidy.js. (Fixes: #1)

- Default to `wss://` as the WebSocket protocol if the page is hosted on
  `https://`. This has no effect if the `webSocketUrl` setting is specified.
  (Pull request: #2)

- Upgrade dependencies.

### 0.4.1 (2014-09-11)

- Update links to point to new independent Mopidy.js GitHub project.

### 0.4.0 (2014-06-24)

- Add support for method calls with by-name arguments. The old calling
  convention, "by-position-only", is still the default, but this will change in
  the future. A warning is printed to the console if you don't explicitly
  select a calling convention. See the docs for details.

### 0.3.0 (2014-06-16)

- Upgrade to when.js 3, which brings great performance improvements and better
  debugging facilities. If you maintain a Mopidy client, you should review the
  [differences between when.js 2 and 3](https://github.com/cujojs/when/blob/master/docs/api.md#upgrading-to-30-from-2x)
  and the
  [when.js debugging guide](https://github.com/cujojs/when/blob/master/docs/api.md#debugging-promises).

- All promise rejection values are now of the Error type. This ensures that all
  JavaScript VMs will show a useful stack trace if a rejected promise's value
  is used to throw an exception. To allow catch clauses to handle different
  errors differently, server side errors are of the type `Mopidy.ServerError`,
  and connection related errors are of the type `Mopidy.ConnectionError`.

### 0.2.0 (2014-01-04)

- **Backwards incompatible change for Node.js users:**
  `var Mopidy = require('mopidy').Mopidy;` must be changed to
  `var Mopidy = require('mopidy');`

- Add support for [Browserify](http://browserify.org/).

- Upgrade dependencies.

### 0.1.1 (2013-09-17)

- Upgrade dependencies.

### 0.1.0 (2013-03-31)

- Initial release as a Node.js module to the
  [npm registry](https://npmjs.org/).
