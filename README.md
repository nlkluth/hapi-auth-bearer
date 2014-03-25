### hapi-auth-bearer

[![Build Status](https://travis-ci.org/j/hapi-auth-bearer.png?branch=master)](https://travis-ci.org/j/hapi-auth-bearer)

#### Bearer authentication

This scheme requires the following options:

- `validateFunc` - Function with signature `function(secretOrToken, callback)` where:
    - `secretOrToken` - the `secret` if option `base64: true` is set, otherwise the raw token value is passed in.
    - `callback` - the callback function with signature `function(err, credentials)` where:
        - `err` - an internal error.
        - `credentials` - a credentials object that gets passed back to the application in `request.auth.credentials`.
          Return `null` or `undefined` to when the credentials are unknown (and not an error).

- `base64` - Boolean value (defaults to `false` aka just accepts a raw token value).  This gives you the ability to pass
 back a base64 encoded authorization header: base64(SECRET:TOKEN)
    - i.e.) Bearer NTJlYjRmZmRmM2M3MjNmZjA1MTEwYmYxOjk5ZWQyZjdmMWRiNjBiZDBlNGY1ZjQ4ZjRhMWVhNWVjMmE4NzU2ZmU=


#####  Using Token
```javascript
var Hapi = require('hapi');
var server = new Hapi.Server();

var credentials = {
  someSuperSecureToken: {
    user: { /** ... */ }
  }
};

var validateFunc = function (token, callback) {
  if (!credentials[token]) {
    callback(null, null);
  } else {
    callback(null, credentials[token]);
  }
};

server.pack.require('hapi-auth-bearer', function (err) {
  server.auth.strategy('bearer', 'bearer', { validateFunc: validateFunc });
});

```

#####  Using Base64 (secret & token)
```javascript
var Hapi = require('hapi');
var server = new Hapi.Server();

var credentials = {
  shhImASecret: {
    token: 'someSuperSecureToken',
    user: { /** ... */ }
  }
};

var validateFunc = function (secret, token, callback) {
  if (!credentials[secret] || credentials[secret].token !== token) {
    callback(null, null);
  } {
    callback(null, credentials[secret]);
  }
};

server.pack.require('hapi-auth-bearer', function (err) {
  server.auth.strategy('bearer-base64', 'bearer', {
    base64: true,
    validateFunc: validateFunc
  });
});

```