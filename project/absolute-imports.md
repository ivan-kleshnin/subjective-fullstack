# Absolute imports

NodeJS notoriously lacks absolute imports. As a result we have to repeat `../` to the point it becomes udreadable:

```js
let KoaDb = require("../../middlewares/db")
let KoaAuthenticate = require("../../middlewares/authenticate")
let KoaAuthorize = require("../../middlewares/authorize")
```

What we wanted is:

```js
let KoaDb = require("backend/middlewares/db")
let KoaAuthenticate = require("backend/middlewares/authenticate")
let KoaAuthorize = require("backend/middlewares/authorize")
```

Besides the visual difference, the second version is more stable.

Imagine dependency graph `a -- b ; b -- c` where `b` imports `a` and `c` imports `b`.
With relative imports:
* if `a` is moved: `a` and `b` should be updated.
* if `b` is moved: `b` and `c` should be updated.

With absolute imports:
* if `a` is moved: only `b` should be updated.
* if `b` is moved: only `c` should be updated.

Despite all the bullshit said [here](https://gist.github.com/branneman/8048520)
the only reasonable way to reproduce absolute imports is **symlinking**.

It keeps every tool (including editors!) working. It keeps things simple.
Modern Windows versions have ways to emulate `ln`. The only thing you may notice – NPM warnings.
The cost of abs. modules "emulation" through an app layer will be much higher, trust me.

You need only:

##### `bin/postinstall`
```sh
ln -sfn ../common node_modules/common
ln -sfn ../backend node_modules/backend
ln -sfn ../frontend node_modules/frontend
ln -sfn ../tests node_modules/tests
```

and

##### `package.json`
```json
{
  "scripts": {
    "postinstall": "bin/postinstall"
  }
}
```

and that's it.
