# 3.1 - Installable Hook
## 1. Where is it?
`node_modules` folder.
## 2. Use case
- Share functionality between Sails apps.
- Publish to NPM for the community.
- *(If creating a hook for 1 app, use project hook.)*
## 3. Creating process:
1. Create a folder with unique name `sails-hook-<your hook name>` (optional but recommended).
2. Create a `package.json` or `npm init` if you are using NPM.
   - If you use `npm init` to create your `package.json`, be sure to manually insert the `sails` key containing `isHook: true`.
        ```js
            "sails": {
            "isHook": true
            }
        ```
3. Write hook code in `index.js`.

## 4. Specifying the internal name Sails uses for your hook (advanced)
### When to use?
In certain cases, especially when using a scoped NPM package to OVERRIDE a core Sails hook, you will want to change the name that Sails uses internally when it loads your hook.
## How to do that?
- Use `sails.hookName` in `package.json`.
- The value SHOULD NOT begin with `sails-hooks-` prefix because it will be loaded into `sails.hooks` dictionary.
```json
{
    "name": "@mycoolhooks/sails-hook-sockets",
    "version": "0.0.0",
    "description": "my own sockets hook",
    "main": "index.js",
    "sails": {
      "isHook": true,
      "hookName": "sockets" // Hook name
    }
}
```
# 3.2 - Project Hook
## 1. Where is it?
- Project hooks are custom Sails hooks that reside in an application’s `api/hooks` folder.

## 2. Use case
- Take advantage of hook features like `defaults` and `routes` for code that is used by multiple components in a single app.
- *If you wish to re-use a hook in more than one Sails app, see creating an installable hook instead.*

## 3. Creating process:
To create a new project hook:
1. Create a folder in `api/hooks` folder.
2. Add an `index.js` file to that foler.
3. Write your hook code in `index.js`

## 4. Check if hook is being loaded
Run `sails lift --verbose`, you will see a message like:

Output: 
verbose: your-hook-name hook loaded successfully.`

# 3.3. Using Hooks in a Sails app
## 1. Using a project hook
To use a project hook in your app, first create the `api/hooks` folder if it doesn’t already exist. Then create the project hook or copy the folder for the hook you want to use into `api/hooks`.

## 2. Using an installable hook
To use an installable hook in your app, simply run `npm install` with the package name of the hook you wish to install (e.g. `npm install sails-hook-autoreload`).

You may also manually copy or link an installable hook folder that you've created directly into your app’s `node_modules` folder.

## 3. Calling hook methods
Using `sails.hooks[<hook-name>]` object.
**For example:**
- The `sails-hook-email` hook provides a `sails.hooks.email.send()` method (note that the `sails-hook-` prefix is stripped off).

## 4. Changing the way Sails loads an installable hook

```js
// config/installedHooks.js
module.exports.installedHooks = {
   "sails-hook-email": {
      // load the hook into sails.hooks.emailHook instead of sails.hooks.email
      "name": "emailHook",
      // configure the hook using sails.config.emailSettings instead of sails.config.email
      "configKey": "emailSettings"
   }
};
```