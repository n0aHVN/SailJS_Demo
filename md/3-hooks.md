# Hooks
`api/hooks/myHook/index.js`

## What is a hook?
- It’s a plugin-like feature that lets you extend or customize the behavior of the Sails framework (Sails core) itself.
- The [hook specification](#5-hook-specification) defines the requirements a module must meet for Sails to be able to import its code and make new functionality available.
- Hooks allow Sails code to be shared between apps and developers.
- TAKE ONLY `sails` object as an only argument. [More at here](#hook-specification).

## Use cases
A Sails hook is a modular piece of code that can:
- Add new functionality to your Sails app (like authentication, logging, etc.)
- Extend or override core features (routes, models, policies, etc.)
- Add custom CLI commands
- Integrate third-party tools or services
*It’s similar in spirit to a plugin in other frameworks.*

Examples:
- Create a reusable logger service
- Inject custom middleware into Sails
- Add a scheduler like node-cron
- Load custom config files
- Modify the request lifecycle

## Hook example:
```js
// api/hooks/my-custom-hook/index.js

module.exports = function defineMyCustomHook(sails) {
  return {
    /**
     * Optional default config
     */
    defaults: {
      __configKey__: {
        enabled: true
      }
    },

    /**
     * Runs when sails loads the hook
     */
    initialize: function(cb) {
      sails.log.info('My custom hook loaded!');
      return cb();
    }
  };
};

```

**To import and use Hook:**
Sails automatically loads hooks from `api/hooks`. You can also disable or configure them in `config/hooks.js`.

```js
module.exports.hooks = {
   "my-custom-hook": true // or false to disable
};

```

## ✅ Hook Lifecycle Methods

|Method	|Purpose|
|--------|-------|
|`defaults`|Define default config for your hook|
|`configure()`|Called after config is loaded but before hook initialization|
|`initialize(cb)`|Main logic; runs when the hook is loaded|
|`routes`|Add or modify routes dynamically|


### `.defaults`
For an example of `default` property: 
Let's say we have an API communicating to another server, you may want to define `timeout` length and `domain` default name.
```json
{
   myapihook: {
      timeout: 5000,
      domain: "www.myapi.com"
   }
}
```

**Namespacing your hook configuration:**
- For [**project hooks**](#types-of-hook) , you should namespace your hook’s configuration under a key that uniquely identifies that hook (e.g. `myapihook` above).
- For [**installable hooks**](#types-of-hook) , you should use the special `__configKey__` key to allow end-users of your hook to change the configuration key if necessary. 
```json
{
   __configKey__: {
      name: "Super Bob"
   }
}
```

### `.configure`
The `configure` feature provides a way to configure a hook after the `defaults` objects have been applied to all hooks. 

`.configure()` runs after:
- Your hook's defaults have been applied
- All other hooks' defaults have been applied
- The user’s config files (e.g. config/*.js) have been loaded.

`.configure()` should:
- Implemented as A FUNCTION.
- NO RETURN ANY VALUE.

**Example:**
- The following configure function could be used for a hook that communicates with a remote API
- It will change the API endpoint based on whether the user set the hook’s `ssl` property to `true`.
- Note that the hook’s configuration key is available in `configure` as `this.configKey`:
```js
configure: function() {

   // If SSL is on, use the HTTPS endpoint
   if (sails.config[this.configKey].ssl == true) {
      sails.config[this.configKey].url = "https://" + sails.config[this.configKey].domain;
   }
   // Otherwise use HTTP
   else {
      sails.config[this.configKey].url = "http://" + sails.config[this.configKey].domain;
   }
}
```
**Misusing `.configure()` in a hook:**
```js
// api/hooks/hook-a/index.js

module.exports = function defineHookA(sails) {
  return {
    defaults: {
      __configKey__: {
        enabled: true
      }
    },

    configure: function () {
      // ❌ BAD: Assumes "hook-b" config is ready
      const hookBConfig = sails.config['hook-b'];

      if (hookBConfig && hookBConfig.enabled) {
        sails.log.info('[hook-a] Hook B is enabled!');
      }
    },

    initialize: function (cb) {
      sails.log.info('[hook-a] Initialized');
      cb();
    }
  };
};

```

The main benefit of `configure` is that all hook `configure` functions are guaranteed to run before any `initialize` functions run; 
Therefore a hook’s `initialize` function can examine the configuration settings of other hooks.


### `.initialize()`

### `routes`

## Hooks vs Services
| Feature/Aspect                            | Services                                                                                          | Hooks                                                                                               |
|------------------------------------------|---------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Can be saved independently of an app     | ❌ No                                                                                              | ✅ Yes                                                                                              |
| Can be reused across different apps      | ❌ Unlikely                                                                                        | ✅ Designed for reuse                                                                              |
| Installed via npm                        | ❌ No                                                                                              | ✅ Yes                                                                                              |
| Initialization system                    | ❌ No                                                                                              | ✅ Has its own init system to configure at lift                                                    |
| Can add routes before Sails lifts        | ❌ No                                                                                              | ✅ Yes                                                                                              |
| Good for shared logic within an app      | ✅ Yes                                                                                             | ✅ Yes (but usually for broader use cases)                                                         |
| Behaves differently per environment      | ❌ Typically not                                                                                   | ✅ Can behave differently (e.g., development vs. production)                                       |
| Best use case                            | Code shared between controllers/models, not reused across apps or environments                    | Reusable, dynamic code that may need routing, config, or environment-specific behavior             |

## Types of hook
There are three types of hooks available in Sails:

- **Core hooks**. 
  - Available to every app. 
  - RARERY need to call core hook.
  - Such as request handling, blueprint route creation, and database integration via Waterline. 
- **Project hooks**. 
  - Live in the `api/hooks` folder of a Sails app.
  - Project hooks provide a way to take advantage of the features of the hook system for code that doesn’t need to be shared between apps.
- **Installable hooks**. 
  - Using `npm` for installation.
  - From Sails community.


## Hook specification
- TAKE ONLY `sails` object as an only argument.
- RETURN AN OBJECT with one or more of the keys described later in this document.

```js
module.exports = function myBasicHook(sails) {
   return {};
}
```
- SAVE in ITS OWN FOLDER and always names file as `index.js` (Ex: /api/hooks/myHook/index.js)


## Custom hook data and functions
- Any other keys added to the object returned from the main hook function will be provided in the `sails.hooks[<hook name>]` object.

 This is how custom hook functionality is provided to end-users. Any data and functions that you wish to remain private to the hook can be added outside the returned object:

 ```js
// File api/hooks/myhook/index.js
module.exports = function myHook(sails) {

   // This var will be private
   var foo = 'bar';

   // This var will be public
   this.abc = 123;

   return {

      // This function will be public
      sayHi: function (name) {
         console.log(greet(name));
      }

   };

   // This function will be private
   function greet (name) {
      return "Hi, " + name + "!";
   }

};
 ```
The public var and function above would be available as:
- `sails.hooks.myhook.abc`
- `sails.hooks.myhook.sayHi` respectively.

