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

You can set all variables at once for a single terminal session. Create a `dev.env` file with a content like:

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

Now load it

```
$ . dev.conf 
```

And run your scripts without a fuss

```
$ npm run foo # MAIL_ROBOT is robot@tsunami.com
...
$ npm run bar # MAIL ROBOT is still robot@tsunami.com
```

Shall you need to switch an environment:

```
$ . dev.test  # source other file
$ npm run foo # MAIL_ROBOT now robot@test.tsunami.com
```

Or do all that at once `$ . dev.conf ; npm run foo` (SHELL will continue to "remember" those variables).

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
exports.RETHINKDB_PORT = parseInt(env.RETHINKDB_PORT) || 28015
exports.RETHINKDB_DB = env.RETHINK_DB || "tsunami"
```

This file serves three purposes:

1. Enumerates all env variables (no need for "sample.conf" reference file)
2. Typecasts all env variables (no need for in place casting etc.)
3. Defaults all env variable (no need for in place value fallbacks)

Or you can just throw on missing ENV variable, preventing potentially invalid runs.

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
# dev.env

# test.env

# .gitignore
  ...
  *.env
```

Decent IDE / editors support "sort-by-type" mode so you don't need a `conf-` prefix to see configuration files sequentially.

### 3. Sensitive data should not be located in the project folder

Project folder is one of the places which can be compromised more easily than others.
Server bug can allow someone to `GET http://yoursite.com/public/../production.conf`.
Some people insist `prod.conf` should be encrypted but then where will you store the encryption key?
Also encryptions can be brute-forced.

So the better solution is to store the whole env inside OS environment variables in production (app `user` specific) 
and possibly rely on more convenient files in other enviroments. 

## Links

https://12factor.net/config

http://security.stackexchange.com/questions/15040/standards-for-encrypting-passwords-in-configuration-files

