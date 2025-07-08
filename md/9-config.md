# config/env/production.js
## Purpose
- This file will be loaded when Sails is running in `production` mode.
- `sails lift --prod`

```js
/**
 * Production environment settings
 *
 * This file can include shared settings for a production environment,
 * such as API keys or remote database passwords.  If you're using
 * a version control solution for your Sails app, this file will
 * be committed to your repository unless you add it to your .gitignore
 * file.  If your repository will be publicly viewable, don't add
 * any private information to this file!
 *
 */

module.exports = {

  /***************************************************************************
   * Set the default database connection for models in the production        *
   * environment (see config/connections.js and config/models.js )           *
   ***************************************************************************/

  // models: {
  //   connection: 'someMysqlServer'
  // },

  /***************************************************************************
   * Set the port in the production environment to 80                        *
   ***************************************************************************/

  // port: 80,

  /***************************************************************************
   * Set the log level in production environment to "silent"                 *
   ***************************************************************************/

  // log: {
  //   level: "silent"
  // }

};
```

# config/locales
This is for different clients in multiple countries

## config/locales/de.json
```json
{
  "Welcome": "Willkommen"
}
```

### How to use it
Use the `req.__()` function:
```js
return res.send(req.__('Welcome'));  // Output: "Willkommen" if German is active
```
#### In Views (EJS files):
```html
<h1><%= __('Welcome') %></h1>
```
#### How does Sails detect the language?
1. The Accept-Language HTTP header from the request
2. Or manually set the locale using:
    ```js
    req.setLocale('de');  // Force German
    ```
### Update the `i18n.js`
```js
module.exports.i18n = {
  locales: ['en', 'de', 'vi'],
  defaultLocale: 'en',
  directory: './config/locales'
};

```
# config/bootstrap.js
- Server-side javascript file that is executed by Sails just before your app is lifted.
```js
/**
 * Bootstrap
 * (sails.config.bootstrap)
 *
 * An asynchronous bootstrap function that runs before your Sails app gets lifted.
 * This gives you an opportunity to set up your data model, run jobs, or perform some special logic.
 *
 * For more information on bootstrapping your app, check out:
 * http://sailsjs.com/documentation/reference/configuration/sails-config-bootstrap
 */

module.exports.bootstrap = function(cb) {

  // It's very important to trigger this callback method when you are finished
  // with the bootstrap!  (otherwise your server will never lift, since it's waiting on the bootstrap)
  cb();
};
```


# config/connection.js
- Contains all the settings for the adapters. (*Adapter in SailJS stands betwen App and DB*).
- We can have multiple settings for multiple DB.

```js
module.exports.connections = {
  localDiskDb: {
    adapter: 'sails-disk'
  },
  mysqlDB: {
    adapter: 'sails-mysql',
    host: 'localhost',
    user: 'root',
    password: '',
    database: 'my_db'
  }
};

```

## To use it:
Default connection for models:
```js
// config/models.js
module.exports.models = {
  connection: 'mysqlDB'
};
```
```js
// api/models/User.js
module.exports = {
  attributes: {
    name: 'string'
  }
  // ← no connection specified here, so it uses the default
};
```

To custom connection per models:
```js
// api/models/Analytics.js
module.exports = {
  connection: 'mongoAnalytics',
  attributes: { ... }
};
```

