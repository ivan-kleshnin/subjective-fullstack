# Environments and Configurations

We share the [Twelve-Factor App](https://12factor.net/config) opinion that platform or library specific configuration 
systems are excessive and process environment should be used instead.

In NodeJS it's `process.env` which can be get as a normal JS object 

```js
console.log(process.env.NODE_ENV)
```

and set like following

```
NODE_ENV=production npm run foo
```

or

```
. .env ; npm run bar
```

The `.env.` file content may look like this:

```
export NODE_ENV=development
export HTTP_PORT=3000
export HTTP_USE_ETAG=no

export SMTP_USERNAME=admin
export SMTP_PASSWORD=
export SMTP_HOST=localhost
export SMTP_PORT=1025
export SMTP_SECURE=no

export MAIL_ROBOT=robot@tsunami.com
export MAIL_SUPPORT=support@tsunami.com
export MAIL_INFO=info@tsunami.com

export RETHINKDB_HOST=localhost
export RETHINKDB_PORT=28015
export RETHINKDB_DB=tsunami
```

The thing to consider is that all shell variables are strings. 
So you'll have to `if (SMTP_SECURE == "yes")` rather than `if (SMTP_SECURE)`.
Such things are displeasing but not enough to justify a [library](https://github.com/lorenwest/node-config).

Our current approach is to have a file `./env.js` with a content like:

```js
let {env} = process

exports.SMTP_USERNAME = env.SMTP_USERNAME || "admin"
exports.SMTP_PASSWORD = env.SMTP_PASSWORD || null
exports.SMTP_HOST = env.SMTP_HOST || "localhost"
exports.SMTP_PORT = parseInt(env.SMTP_PORT) || 1025
exports.SMTP_SECURE = env.SMTP_SECURE == "yes"

...

exports.RETHINKDB_HOST = env.RETHINKDB_HOST || 8080
exports.RETHINKDB_PORT = parseInt(env.RETHINK_DB) || 28015
exports.RETHINKDB_DB = env.RETHINK_DB || "tsunami"
```

This file serves three purposes:

1. Enumerates all env variables (no need for "sample.conf" reference file)
2. Typecast all env variables (no need for in place casting etc.)
3. Default all env variable (no need for in place value fallbacks)

## Where to store passwords

Things to consider.

### 1. Sensitive data should be kept outside of `/public/`

Conclusion: use `public` name as it reminds of security (better than `static`).

### 2. Sensitive data should be hard to commit accidentally

Conclusion: store such data in files with predefined extension which is used then in `.gitignore`.<br/>
Do not enumerate them one by one (easy to forget new ones). Use catchall style.<br/>

#### Bad

```
# .conf-dev

# .conf-test

# .gitignore
  ...
  .conf-dev
  .conf-test
```

#### Better

```
# dev.conf

# test.conf

# .gitignore
  ...
  *.conf
```

Decent IDE / editors support "sort-by-type" mode so you don't need a `conf-` prefix to see configuration files sequentially.

### 3. Sensitive data should not be located in the project folder

Project folder is one of the places which can be compromised more easily than others.
Server bug can allow someone to `GET http://yoursite.com/public/../production.conf`.
Some say `production.conf` should be encrypted but then where will you store the encryption key?
Also encryptions can be brute-forced.

So the better solution is to store the whole env inside OS environment variables (app `user` specific) and 
possibly rely on files on dev machines.

## Links

https://12factor.net/config

http://security.stackexchange.com/questions/15040/standards-for-encrypting-passwords-in-configuration-files

