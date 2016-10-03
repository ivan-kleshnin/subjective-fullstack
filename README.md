# Checklist

- [ ] Architecture
  - [ ] Folder structure
  - [ ] Absolute imports
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

`backend/` – node app. Typically contains separate `app.js` (exports app) and `server.js` (serves app).

`bin/` – CLI scripts too big to fit into `package.json`.

`common/permissions/` – a list of permissions by role (or group). I'm pretty convinced a.t.m. that putting permissions in DB is a bad idea. The more stuff you have under VCS – the better. Code provides much better control and bug-safety than DB (in general).

`common/types/` – Tcomb / TypeScript / Flow types. "Type" files also include type-specific but business-logic-generic helpers. 
For example `User.js` can expose `export let isPaidClient = (user) => user.activated && !user.blocked && user.paidUntil > new Date()`

`crons/` – one-structure-fits-all approach. Write your app code in project, not in crontabs.

`frontend/` – browser app + styles + (non-content) images + fonts, etc.

`ignore/` – under `.gitignore`. I put random temporary stuff here like unfinished docs, etc.

`public/` – better than `static/` because: 1) reminds of security 2) "static" files are in `frontend/` (or `src/`) as well.

`tests/` – better than `specs/` as the latter is obscure . An app can have real, human-readable specs.
