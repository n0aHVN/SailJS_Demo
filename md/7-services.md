# api/services
## Purpose
- Any logic doesn't necessarily have to happen during the user sends a request and when the server sends back a response is a `Service`.
- Doesn't rely on `.req()` and `.res()`
```js
module.exports = {
  send: function(to,from,body){
    // fancy code that sends an email
  }
}
```
To use it:
```js
Email.send('rick','bill','lol')
```