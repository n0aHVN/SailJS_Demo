# Hooks
`api/hooks/myHook/index.js`

## What is a hook?
- A Node module adding functionality to the Sails core
- The [hook specification](#5-hook-specification) defines the requirements a module must meet for Sails to be able to import its code and make new functionality available.
- Hooks allow Sails code to be shared between apps and developers.
- TAKE ONLY `sails` object as an only argument. [More at here](#hook-specification)

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

## Hook Features
All features are optional:

### `.defaults`


### `.configure`

### `.initialize()`

### `routes`


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
- `sails.hooks.myhook.sayHi`respectively.
