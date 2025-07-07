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
      "hookName": "sockets"
    }
}
```
# 3.2 - Project Hook