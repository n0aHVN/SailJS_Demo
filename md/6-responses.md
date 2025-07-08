# api/responses
## Purpose
This folder holds the logic for issuing server responses to your clients.

**To create a custom response:**
```sails-generate-custom-response```

# badRequest.js
```js
/**
 * 400 (Bad Request) Handler
 *
 * Usage:
 * return res.badRequest();
 * return res.badRequest(err);
 * return res.badRequest(err, view);
 * return res.badRequest(err, redirectTo);
 *
 * e.g.:
 * return res.badRequest(
 *   'Please choose a valid `password` (6-12 characters)',
 *   '/trial/signup'
 * );
 *
 */

module.exports = function badRequest(err, viewOrRedirect) {

  // Get access to `req` & `res`
  var req = this.req;
  var res = this.res;

  // Serve JSON (with optional JSONP support)
  function sendJSON (data) {
    if (!data) {
      return res.send();
    }
    else {
      if (typeof data !== 'object' || data instanceof Error) {
        data = {error: data};
      }
      if ( req.options.jsonp && !req.isSocket ) {
        return res.jsonp(data);
      }
      else return res.json(data);
    }
  }

  // Set status code
  res.status(400);

  // Log error to console
  this.req._sails.log.verbose('Sent 400 ("Bad Request") response');
  if (err) {
    this.req._sails.log.verbose(err);
  }

  // If the user-agent wants JSON, always respond with JSON
  if (req.wantsJSON) {
    return sendJSON(err);
  }

  // Make data more readable for view locals
  var locals;
  if (!err) { locals = {}; }
  else if (typeof err !== 'object'){
    locals = {error: err};
  }
  else {
    var readabilify = function (value) {
      if (sails.util.isArray(value)) {
        return sails.util.map(value, readabilify);
      }
      else if (sails.util.isPlainObject(value)) {
        return sails.util.inspect(value);
      }
      else return value;
    };
    locals = { error: readabilify(err) };
  }

  // Serve HTML view or redirect to specified URL
  if (typeof viewOrRedirect === 'string') {
    if (viewOrRedirect.match(/^(\/|http:\/\/|https:\/\/)/)) {
      return res.redirect(viewOrRedirect);
    }
    else return res.view(viewOrRedirect, locals, function viewReady(viewErr, html) {
      if (viewErr) return sendJSON(err);
      else return res.send(html);
    });
  }
  else return res.view('400', locals, function viewReady(viewErr, html) {
    if (viewErr) return sendJSON(err);
    else return res.send(html);
  });
};
```

## Explaination
```js
function sendJSON (data) {
  if (!data) {
    return res.send();
  }
  else {
    if (typeof data !== 'object' || data instanceof Error) {
      data = {error: data};
    }

    if ( req.options.jsonp && !req.isSocket ) {
      return res.jsonp(data);
    }
    else return res.json(data);
  }
}
```
- If no data, send an empty response.
- If data is an error or not an object, wrap it as { error: data }.
- If JSONP is enabled and it's not a socket request, return JSONP.
- Otherwise, return regular JSON.

**Prepare `locals` for HTML view rendering**
```js
var locals;
if (!err) { locals = {}; }
else if (typeof err !== 'object'){
  locals = {error: err};
}
else {
  var readabilify = function (value) {
    if (sails.util.isArray(value)) {
      return sails.util.map(value, readabilify);
    }
    else if (sails.util.isPlainObject(value)) {
      return sails.util.inspect(value);
    }
    else return value;
  };
  locals = { error: readabilify(err) };
}

```
- Prepares data (locals) to be passed to the HTML view.
- Makes sure err is readable (e.g., inspects objects or arrays for display in view).

## Using
```js
// With just a message
return res.badRequest('Missing password');

// With message and redirect
return res.badRequest('Invalid email', '/signup');

// With custom view
return res.badRequest(err, 'errors/invalid-data');
```

## ok.js
```js
/**
 * 200 (OK) Response
 *
 * Usage:
 * return res.ok();
 * return res.ok(data);
 * return res.ok(data, view);
 * return res.ok(data, redirectTo);
 * return res.ok(data, true);
 *
 * @param  {Object} data
 * @param  {Boolean|String} viewOrRedirect
 *         [optional]
 *          - pass string to render specified view
 *          - pass string with leading slash or http:// or https:// to do redirect
 */

module.exports = function sendOK (data, viewOrRedirect) {

  // Get access to `req` & `res`
  var req = this.req;
  var res = this.res;

  // Serve JSON (with optional JSONP support)
  function sendJSON (data) {
    if (!data) {
      return res.send();
    }
    else {
        // /It checks if the data is a primitive value (like a string, number, boolean, etc.) and, if so, just sends it as-is using:    
      if (typeof data !== 'object') { return res.send(data); }
      if ( req.options.jsonp && !req.isSocket ) {
        return res.jsonp(data);
      }
      else return res.json(data);
    }
  }

  // Set status code
  res.status(200);

  // Log to console
  this.req._sails.log.verbose('Sent 200 ("OK") response');
  if (data) {
    this.req._sails.log.verbose(data);
  }

  // Serve JSON (with optional JSONP support)
  if (req.wantsJSON) {
    return sendJSON(data);
  }

  // Make data more readable for view locals
  var locals;
  if (!data || typeof data !== 'object'){
    locals = {};
  }
  else {
    locals = data;
  }

  // Serve HTML view or redirect to specified URL
  if (typeof viewOrRedirect === 'string') {
    if (viewOrRedirect.match(/^(\/|http:\/\/|https:\/\/)/)) {
      return res.redirect(viewOrRedirect);
    }
    else return res.view(viewOrRedirect, locals, function viewReady(viewErr, html) {
      if (viewErr) return sendJSON(data);
      else return res.send(html);
    });
  }
  else return res.view(locals, function viewReady(viewErr, html) {
    if (viewErr) return sendJSON(data);
    else return res.send(html);
  });

};
```

## Other response is the same