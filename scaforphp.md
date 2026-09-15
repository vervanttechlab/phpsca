# ScaPExpress — Prefix Shortlist (PHP / MySQL, vanilla OOP)

Type a prefix and press Tab. Code snippets fire in `.php` and `.sql` files; the
cheat-sheet snippets fire in markdown, plain text, shell, `.env` (`dotenv`),
`.gitignore` (`ignore`) and `properties` files.

**In a `.php` file, always type `<?php` on line 1 first, press Enter, then type
the prefix on the next line.** Example, adding the create endpoint:

```php
<?php            <- 1. type this and press Enter
api/create       <- 2. type the prefix here, press Tab
```

Without the `<?php` line nothing appears - see the rule below.

Wherever a prefix plays the same role as one in ScaExpress, it is unchanged -
`sx-db`, `sx-auth-middleware`, `api/register`, `api/create`, and so on. Only
five prefixes are new here (`sx-router`, `sx-request`, `sx-response`, `sx-jwt`,
`sx-env-loader`), for the Core plumbing classes PHP needs that Node gets for
free from Express. Three Node prefixes have no PHP counterpart on purpose:
`sx-routes` and `sx-routes-auth` (there is no separate route file - routes are
registered straight on the Router in `public/index.php`) and `express-server`
(renamed `front-controller`, the second alias of `sx-server`).

## The one rule for `.php` files: the file must start with `<?php`

**No `<?php` on line 1 = no PHP snippets. None of them will appear.** VS Code
treats everything before `<?php` as HTML, and only offers a language's snippets
when the cursor is inside that language's region. This is the number-one reason
"the prefix does nothing":

```php
api/register        <- a file containing only this: nothing appears (HTML region)
```

```php
<?php
api/reg             <- now the suggestion appears; press Tab
```

The same for every other `.php` prefix - `sx-db`, `api/create`, `sx-controller`:

```php
<?php
api/create          <- suggestion appears; Tab inserts the transaction-based create()
```

So in every new `.php` file (`test.php` included): type `<?php`, press Enter,
and type the prefix on the line **below** the tag. Every placeholder the
extension writes already has the tag on line 1 - put the cursor on the line
under it and type. The only file that comes out empty is
`src/Config/Database.php`: type `<?php`, Enter, then `sx-db`.

Two more things that hide the popup:

- Typing the `/` in `api/register` closes the suggestion list (it is not a
  word character). Keep typing - `api/reg` brings it back - or press
  `Ctrl+Space` (Mac `⌃Space`). The same snippet also answers to `sx-register`
  and `register-endpoint`, which have no slash.
- VS Code does not auto-suggest inside a comment. Do not type on the
  `// Not written yet ...` placeholder line; use the line below it.

Since 0.1.3 the class and file snippets (`sx-db`, `sx-auth-middleware`,
`sx-router`, `sx-request`, `sx-response`, `sx-env-loader`, `sx-jwt`,
`sx-controller`, `sx-server`, `sx-server-min`) no longer insert their own
`<?php`, so the tag you typed is never doubled.

## Where each prefix goes (in build order)

| Day 2 step | File | Type | Then |
| --- | --- | --- | --- |
| Day 1 | `database/schema.sql` | `sx-schema` | run it; `sx-db-user` in the same file for the non-root user |
| Day 1 | `.env` | `env` | set `DB_USER` / `DB_PASS` to the user you just created |
| Day 1 | `.gitignore` | `ignore` | |
| 2 | `src/Core/Env.php` | `<?php` then `sx-env-loader` | written for you by Create Folder Structure; type it only when practising by hand |
| 2 | `src/Config/Database.php` | `<?php` then `sx-db` | test: `php -r "require 'autoload.php'; App\Core\Env::load('.env'); App\Config\Database::getConnection(); echo 'ok';"` |
| 3 | `src/Core/Response.php` | `<?php` then `sx-response` | written for you, same as Env |
| 3 | `public/index.php` | `<?php` then `sx-server-min` (the placeholder already has the tag) | `php -S localhost:3000 -t public`, open `/` |
| 4-6 | `src/Controllers/AuthController.php` | `<?php`, `sx-controller` (rename the class to `AuthController`), then `api/register` and `api/login` inside the braces | `sx-controller` already imports `Database`, `Env`, `Jwt`, `Request`, `Response` - nothing to add for login |
| 5 | `src/Core/Request.php`, `src/Core/Router.php` | `<?php` then `sx-request` / `sx-router` | written for you |
| 5 | `public/index.php` | uncomment the `AuthController` use line, `$auth = new …` and the two `/auth` routes | Postman: register, then login, copy the token |
| 6 | `src/Core/Jwt.php` | `<?php` then `sx-jwt` | written for you |
| 7 | `src/Middlewares/AuthMiddleware.php` | `<?php` then `sx-auth-middleware` (placeholder already has the tag) | |
| 8-11 | `src/Controllers/ItemController.php` | **Add Resource** command (writes all six methods and wires the routes) - or by hand: `<?php`, then `sx-controller`, then inside the braces `api/get`, `api/create`, `api/get-id`, `api/summary`, `api/update`, `api/delete` | if written by hand, accept the "Wire it in" prompt so `/summary` lands above `/:id` |
| 9 | inside `create()` | `sx-transaction` | only if you are writing the method yourself instead of `api/create` |
| 12 | `public/index.php` | select all, type `<?php`, Enter, then `sx-server` | headers, CORS, static files, 404, error handler |
| 13 | `getAll()` in ItemController | replace it with `api/search` | optional filters |
| Day 1 | `docs/*.md` | `docs-arch`, `docs-setup`, `docs-api`, `docs-trouble` | |

## Code snippets — `.php` files (type `<?php` on line 1 first, then the prefix on line 2)

| Prefix | Gives you |
| --- | --- |
| `api/register`, `register-endpoint`, `sx-register` | POST /auth/register - duplicate check, password_hash, 201 response |
| `api/login`, `login-endpoint`, `sx-login` | POST /auth/login - password_verify, JWT with expiry, 401 path |
| `api/get`, `api/view`, `api/read`, `getall-endpoint`, `sx-getall` | GET collection - joins the owner and the lookup table, newest first |
| `api/search`, `api/filter`, `getall-filtered` | GET collection - optional $_GET filters (search, category, min/max price), still parameterized |
| `api/get-id`, `api/getbyid`, `api/view-id`, `sx-getone` | GET /:id - single record with the not found path |
| `api/create`, `api/post`, `create-endpoint`, `sx-create` | POST - writes the record and an activity log in one PDO transaction |
| `api/update`, `api/put`, `update-endpoint`, `sx-update` | PUT /:id - unsent fields keep their existing value |
| `api/delete`, `delete-endpoint`, `sx-delete` | DELETE /:id - rowCount() check drives the 404 |
| `api/summary`, `api/report`, `sx-summary` | GET /summary - COUNT, SUM, AVG, MAX grouped by category with HAVING |
| `api/form`, `sx-classic-form` | Handles a supplied `<form action method=POST>`: maps its field names and redirects |
| `sx-db`, `db-pool`, `mysql-pool` | One shared PDO connection per request, MySQL DSN read from Env |
| `sx-auth-middleware`, `auth-middleware`, `jwt-middleware` | Verifies Authorization: Bearer <token> and attaches the decoded user |
| `sx-router`, `router-class` | Matches method+path top to bottom, same order rule as Express |
| `sx-request`, `request-class` | Bundles route params, $_GET and the parsed JSON body, plus $user from AuthMiddleware |
| `sx-response`, `response-class` | One place that sets the status code, sets JSON, echoes and stops |
| `sx-env-loader`, `env-class` | Reads KEY=VALUE lines from .env into the environment - PHP has no dotenv built in |
| `sx-jwt`, `jwt-class` | hash_hmac HS256 encode/decode - no Composer package needed |
| `sx-controller`, `controller-header` | Namespace and the use lines every controller starts with (Database, Env, Jwt, Request, Response, Throwable) - so `api/login` pasted into it just works |
| `sx-server`, `front-controller` | Security headers, CORS, health check, static files, routes, 404 and error handler |
| `sx-transaction`, `db-transaction` | beginTransaction, commit, rollBack - no separate connection or release() to remember |
| `sx-fn`, `controller-fn` | Empty method with the Request parameter every handler takes |
| `sx-static`, `serve-frontend` | The php -S equivalent of express.static: return false and let the server serve the file |
| `sx-cors`, `cors-config` | Allows the origin plus the custom headers the preflight checks - hand-rolled, no cors package |
| `sx-server-min`, `server-min` | The smallest front controller that runs: autoload, Env::load, health check, 404 |

## Code snippets — `.sql` files

| Prefix | Gives you |
| --- | --- |
| `sx-db-user`, `db-user`, `create-user` | CREATE USER + GRANT scoped to one database - run this before pointing .env at anything but root |
| `sx-schema`, `schema-crud` | Users, categories, a CRUD table and an activity log, with foreign keys and seed data |
| `sx-table`, `table-fk` | One resource table linked to users and categories |
| `sx-join`, `select-join` | Owner with INNER JOIN, lookup with LEFT JOIN, aliased for the front end |
| `sx-groupby`, `select-summary` | COUNT, SUM, AVG and MAX per category, filtered after grouping |

## Cheat-sheet snippets — markdown / plaintext / shell / dotenv / ignore / properties

| Prefix | Gives you |
| --- | --- |
| `pack`, `sx-pack`, `packages` | No packages to install - verify the PHP version and the pdo_mysql extension instead |
| `pack1`, `sx-pack-oneline` | The same check as a single line |
| `pack-why`, `sx-pack-why` | Table of PHP core features and the Node package each one replaces, for the oral questioning |
| `s0`, `init`, `sx-init`, `start` | Folder and git, in order - no npm init, nothing to install |
| `struct`, `sx-struct`, `structure` | The OOP MVC layout, with what each folder is responsible for |
| `files`, `sx-files`, `mkdir` | Commands that create the whole tree |
| `steps`, `sx-steps`, `order` | The order to build in, each step testable before the next |
| `s1`, `sx-step-setup` | What to do and how to know it worked |
| `s2`, `sx-step-db` | Schema first, because nothing is testable without tables |
| `s3`, `sx-step-connection` | PHP has no autoloading or dotenv built in - write both before any controller |
| `s4`, `sx-step-server` | The smallest runnable front controller, so everything after is testable |
| `s5`, `sx-step-auth` | Register, then login, then the guard |
| `s6`, `sx-step-crud` | Build the resource in the order that keeps every step testable |
| `s7`, `sx-step-finish` | Security headers, handlers, documentation and commits |
| `env`, `sx-env`, `dotenv` | Contents of .env - read by the hand-written Env class, not by a package |
| `ignore`, `sx-gitignore` | Contents of .gitignore - no vendor/ or node_modules/, vanilla PHP has neither |
| `test`, `sx-test`, `smoke` | curl commands that walk the whole API, including the failure cases |
| `docs-arch`, `sx-docs-architecture` | SYSTEM_ARCHITECTURE.md skeleton |
| `docs-setup`, `sx-docs-setup` | SETUP_INSTRUCTIONS.md skeleton |
| `docs-api`, `sx-docs-api` | API_DOCS.md skeleton with the endpoint table |
| `docs-trouble`, `sx-docs-troubleshooting` | TROUBLESHOOTING.md skeleton |
