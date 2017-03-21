# Absolute imports

NodeJS notoriously lacks absolute imports. As a result we have to repeat `../` to the point it becomes unreadable:

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

Besides being more obvious, the second version is more sustainable.

Imagine the next graph of modular dependencies (all app specific i.e relative):

```
X depends on something
Y depends on X
Z depends on X and Y
```

Now with relative imports:
* if `X` is moved: `X`, `Y`, `Z` should be updated.
* if `Y` is moved: `Y`, `Z` should be updated.
* if `Z` is moved: `Z` should be updated

And with absolute imports:
* if `X` is moved: `Y`, `Z` should be updated.
* if `Y` is moved: `Z` should be updated
* if `Z` is moved: –

The second case is unconditionally better.

If you ignore all the fancy bullshit proposed [here](https://gist.github.com/branneman/8048520)
the only reasonable way to reproduce absolute imports stays **symlinking**.

It keeps every tool (linters, editors!) working. It keeps things simple.
Even modern Windows versions have ways to emulate `ln`. The only thing you may notice – NPM warnings.
The cost of import spoofings on the App layer would be *much* higher.

How to do that? Very simple:

##### bin/install

```sh
ln -sfn ../common node_modules/common
ln -sfn ../backend node_modules/backend
ln -sfn ../frontend node_modules/frontend
ln -sfn ../tests node_modules/tests
```

Just don't forget to invoke this script among the first steps of the deployment process.
