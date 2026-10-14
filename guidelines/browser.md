# Browser Tests (pest-plugin-browser)

- Browser tests live in `tests/Browser/` as plain Pest tests using the pest-plugin-browser (Playwright) engine; the plugin serves the app in-process over `:memory:` SQLite.
- Run the whole browser suite with `vendor/bin/sail bin pest --parallel tests/Browser`. Do not use `vendor/bin/sail artisan test` for browser-only runs.
- Do not run two browser suites concurrently: the plugin keeps ONE shared Playwright handshake file (`vendor/pestphp/pest-plugin-browser/.temp/playwright-server.json`), so overlapping runs destroy each other's server.
- Wait for asynchronously rendered content with the `waitFor` / `waitForMissing` helpers the repository defines in its Pest setup (`text=` exact, `text~=` contains, plus any `@`-shorthand it registers); Playwright assertions are strict — use `:nth-match(..., 1)` when a selector matches several elements.
- **Method chaining**: the general chaining rule applies to `$browser` chains too. Keep the first method call on the same line: `$browser = visit(...)->assertSee(...)`, not `$browser\n->assertSee(...)`.
- **File uploads** cannot go through the browser (the in-process server never parses multipart bodies): create the media through the model/HTTP-testing API in the same test and let the browser assert the UI around it. Use `->preservingOriginal()` when attaching a committed fixture file if the media library in use unlinks the source by default.
