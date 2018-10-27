# WebAuth

A HTTP proxy that requires authentication before allowing requests through.

[Inteface screenshots](https://cloudup.com/cU0jWGvAfit)

## Installation

```
$ npm install --production
```

## Running

```
$ node server.js
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

Backends are listed in the backends json file (must be readable by the application process).

```json
{
    "title": "WordPress",
    "description": "Hidden WordPress instance",
    "url": "/wordpress/",
    "options": {
        "target": "http://127.0.0.1:5060/"
    }
}
```

-   **title** is the backend title. This field should be set as it is the link text in backend listing
-   **description** is an optional description text
-   **url** is the URL prefix for that backend, it should start and end with a slash. If the value is "/test/", then the backend is available from `http://proxyhostname/test/`
-   **options** is the proxy configuration, see all avialble options [here](https://www.npmjs.com/package/http-proxy#options). In most cases you probably want to set these.

For example if you want to modify 301 and 302 redirects and also set your own Host: header, then the options object could look like this:

```json
{
    "target": "http://127.0.0.1:5060/",
    "headers": {
        "host": "wordpress.tahvel.info"
    },
    "autoRewrite": true
}
```

## Running as systemd service

Assuming you have this application copied to `/opt/auth-proxy`

```
$ cd /opt/auth-proxy
$ npm install --production
```

**Step 1.** Create a proxy user

This would be an unprivileged user

```
$ useradd auth-proxy
```

**Step 2.** Create a config folder

```
$ mkdir /etc/auth-proxy
$ cp config/* /etc/auth-proxy
$ cp test/users.json /etc/auth-proxy
$ mv /etc/auth-proxy/default.toml /etc/auth-proxy/auth-proxy.toml
$ chown auth-proxy:auth-proxy /etc/auth-proxy/users.json
```

> **NB!** edit _/etc/auth-proxy/auth-proxy.toml_ and set user db location to `"/etc/auth-proxy/users.json"`

**Step 3.** Enable nginx virtual host

```
$ cp test/auth-proxy-nginx.conf /etc/nginx/sites-available/auth-proxy
$ ln -s /etc/nginx/sites-available/auth-proxy /etc/nginx/sites-enabled/auth-proxy
$ systemctl reload nginx
```

> **NB!** change the virtual host server name in _/etc/nginx/sites-enabled/auth-proxy_ before actually using it

**Step 4.** Set up systemd service

```
$ cp test/auth-proxy.service /etc/systemd/system/auth-proxy.service
$ systemctl enable auth-proxy
$ systemctl start auth-proxy
```

Next open the hostname of the proxy service in your browser. Default username is "admin" and password is "admin" (or whatever you have set in the users config file).

## Update

Just replace the files in _/opt/auth-proxy_. If you are using configuration from _/etc/auth-proxy_ then replacing application files should not mess with your existing setup.

## License

**ISC**
