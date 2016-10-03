# Fullstack App Checklist

- [ ] Architecture
  - [x] Folder structure
  - [x] Absolute imports
  - [ ] Purely functional code ([Ramda](http://ramdajs.com))
  - [ ] Component approach
  - [ ] Frontend dataflow
  - [ ] Backend REST API
  - [ ] Frontend REST API
  - [ ] I18n, L10n
  - [ ] Validation
    - [ ] Validation rules sharing
  - [ ] Multiple environments / configuration management
  - [ ] Multiple frontend entry points
  - [ ] Frontend routing
  - [ ] Define frontend variables from backend / bundler
  - [ ] CORS support
  - [ ] GZIP support
  - [ ] DB pooling
  - [ ] AJAX
    - [ ] Request queue
    - [ ] Bad connection handling / retries
    - [ ] Progress indication
  - [ ] Notifications
  - [ ] Forms
    - [ ] Error messages
    - [ ] Debounce
    - [ ] Double submit prevention
  - [ ] Promises
  - [ ] URL bound and URL unbound pages
  - [ ] CRUD
    - [ ] Create
    - [ ] Detail
    - [ ] Edit
    - [ ] Remove
    - [ ] Inline edit
    - [ ] Drag & Drop example
  - [ ] Index
    - [ ] Filters
    - [ ] Sorts
    - [ ] Perpage
    - [ ] Pagination
    - [ ] Frontend-only filtering, sorting, perpage (whenever possible)
    - [ ] Example of infinite scroll
    - [ ] Example of masonry
  - [ ] Isomorphic app
  - [ ] Frontend DB (query language)
  - [ ] Optimistic updates
  - [ ] CSRF protection

- [ ] Bundling
  - [ ] Webpack
  - [ ] Bundle minification (prod)
  - [ ] Bundle uglification (prod)
  - [ ] Bundle vendor splitting (dev)
  - [ ] Source Maps (dev)
  - [ ] Auto inline assets (prod)
  - [ ] Cache management
  - [ ] Skip already minified files
  - [ ] Assets deduplication

- [ ] Development
  - [ ] Backend server auto-reload
  - [ ] Frontend live reload (dev)
  - [ ] Frontend hot reLoad (dev)

- [ ] Code Quality
  - [ ] Linting
  - [ ] Unit tests
  - [ ] Functional tests

- [ ] Undecided
  - [ ] CSS modules vs Local styles
  - [ ] Real time (suspend for now)

## Architecture

### Folder Structure

Must not be a dogma. Here's my typical stuff just for the reference:

```
backend/
bin/      
common/   
  permissions/
    admin.js
    ...
    visitor.js
  types/    
    Id.js
    ...
    User.js
crons/    
  hourly.js 
  daily.js  
  ...  
frontend/ 
ignore/   
public/   
tests/    
```

#### `backend/`

Node app. Typically contains separate `app.js` (exports app) and `server.js` (serves app).

#### `bin/` 

CLI scripts too big to fit into `package.json`.

#### `common/permissions/` 

A list of permissions by role (or group). I'm pretty convinced a.t.m. that putting permissions in DB is a bad idea. The more stuff you have under VCS – the better. Code provides much better control and bug-safety than DB (in general).

#### `common/types/` 

Tcomb / TypeScript / Flow types. "Type" files also include type-specific but business-logic-generic helpers. 

For example `User.js` can contain

```
export let User = T.struct({
  id: Id,
  email: Email,
  ...
})

export let isPaidClient = (user) => user.activated && !user.blocked && user.paidUntil > new Date()
```

#### `crons/` 

One-structure-fits-all approach. Write your app code in project, not in crontabs.

#### `frontend/` 

Browser app + styles + (non-content) images + fonts, etc.

#### `ignore/` 

Under `.gitignore`. I put random temporary stuff here like unfinished docs, etc.

#### `public/` 

Better than `static/` because: 1) reminds of security 2) "static" files are in `frontend/` (or `src/`) as well.

#### `tests/` 

Better than `specs/` as the latter is obscure . An app can have real, human-readable specs.

### Absolute imports

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
Present Windows version have solutions to emulate `ln`. The only thing you may notice – NPM warnings.
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
