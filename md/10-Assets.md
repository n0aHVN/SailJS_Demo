# Assets
## Overview
- Static files (js, css, image, html,...)
- When lift app, Sails' built-in pipeline processes and syncs those files to a hidden folder (`.tmp/public/`).

## Static Middleware
- BTS, Sails uses the static middleware from Express to serve assets.
- Configure this middleware in `/config/http.js`
### `index.html`
- Create `assets/foo.html`, it willl be accessed at `http://localhost:1337/foo.html`
- Create `assets/foo/index.html`, accessed both at `http://localhost:1337/foo/index.html` and `http://localhost:1337/foo`

### Precedence (Priority)
If:
- You define a custom route
- Have a file in `assets` directory with a same path.

=>  Sails JS will choose Custom Route

**For example:**
- If you create `assets/index.html`, with no routes defined in your `config/routes.js`, it will be served as your home page.
- But if you define a custom route, `'/': 'FooController.bar'`, that route will take **precedence**.