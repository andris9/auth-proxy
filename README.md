# Auth Proxy

A HTTP proxy that requires authentication.

## Installation

```
$ npm install --production
```

## User accounts

User accounts are stored in a json file on disk. File location can be set in the config file. This file must be both readable and writable by the application process.

User account entry includes a password field. By default it can contain a plaintext password. Once user account is updated via web interface, plaintext password is converted to a pbkdf2-sha512 password hash. Users added via web interface also get a hashed password to the user entry. If you forget your password, then just replace the password hash string in the json file with some plaintext password to log in.

```json
{
    "admin": {
        "enabled": true,
        "tags": ["admin"],
        "password": "admin"
    }
}
```

Users with the `admin` tag can add and modify other users. Non-admin users can only access hidden backends.

## Backends

Backends are listed in the backends json file (can be only readable by the application process).

```json
{
    "title": "Test",
    "description": "Proovin lihtsalt niisama",
    "url": "/test1/",
    "options": {
        "target": "http://127.0.0.1:5060/"
    }
}
```

-   **title** is the backend title. This field should be set as it is the link text in backend listing
-   **description** is an optional description text
-   **url** is the URL prefix for that backend, it should start and end with a slash. If the value is "/test1/", then the backend is available from `http://proxyhostname/test1/`
-   **options** is the proxy configuration, see all avialble options [here](https://www.npmjs.com/package/http-proxy#options)

Fro example if you want to modify 301 and 302 redirects and also set your own Host: header, then the options objec could look like this:

```json
{
    "target": "http://127.0.0.1:5060/",
    "headers": {
        "host": "wordpress.tahvel.info"
    },
    "autoRewrite": true
}
```

## License

**ISC**
