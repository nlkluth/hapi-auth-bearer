### hapi-auth-bearer

[![Build Status](https://travis-ci.org/j/hapi-auth-bearer.png?branch=master)](https://travis-ci.org/j/hapi-auth-bearer)

#### Bearer authentication

This scheme requires the following options:

- `validateFunc` - Function with signature `function(secretOrToken, callback)` where:
    - `secretOrToken` - the `secret` if option `base64: true` is set, otherwise the raw token value is passed in.
    - `callback` - the callback function with signature `function(err, credentials)` where:
        - `err` - an internal error.
        - `credentials` - a credentials object that gets passed back to the application in `request.auth.credentials`.
          Return `null` or `undefined` to when the credentials are unknown (and not an error).  Also, credentials
          object MUST contain a key `token` when using `base64: true` option so that we can validate the header
          token from the token stored in the database. (This makes it so that you only need to index `secret` instead
          of both `secret` and `token` for smaller storage.  Just simply do a find on secret, and pass back the token
          value.

- `base64` - Boolean value (defaults to `false` aka just accepts a raw token value).  This gives you the ability to pass
 back a base64 encoded authorization header: base64(SECRET:TOKEN)
    - i.e.) Bearer NTJlYjRmZmRmM2M3MjNmZjA1MTEwYmYxOjk5ZWQyZjdmMWRiNjBiZDBlNGY1ZjQ4ZjRhMWVhNWVjMmE4NzU2ZmU=


```javascript
var Hapi = require('hapi');
var server = new Hapi.Server();

var credentials = {
  // without base64
  someSuperSecureToken: {
    user: { /** ... */ }
  },
  // for base64
  shhImASecret: {
    token: 'someSuperSecureToken',
    user: { /** ... */ }
  }
};

var validateFunc = function (id, callback) {
  return callback(null, credentials[id]);
};

server.pack.require('hapi-auth-bearer', function (err) {
  server.auth.strategy('bearer', 'bearer', { validateFunc: validateFunc });

  server.auth.strategy('bearer-base64', 'bearer', {
    base64: true,
    validateFunc: validateFunc
  });
});

```