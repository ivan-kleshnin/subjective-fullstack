# Project structure

Must not be a dogma. Here's my typical stuff just for the reference:

```
bin/
client/
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
ignore/
public/
server/
tests/
```

#### `server/`

Server-side app. Typically contains separate `app.js` (exports app) and `server.js` (serves app).

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

#### `client/`

Browser app + styles + (non-content) images + fonts, etc.

#### `ignore/`

Under `.gitignore`. I put random temporary stuff here like unfinished docs, etc.

#### `public/`

Better than `static/` because: 1) reminds of security 2) "static" files are in `frontend/` (or `src/`) as well.

#### `tests/`

Better than `specs/` as the latter is obscure . An app can have real, human-readable specs.



