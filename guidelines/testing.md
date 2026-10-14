# Test Execution

The preferred way to run tests in Sail projects is `vendor/bin/sail artisan test --compact --parallel` — with both flags, for full-suite and filtered runs alike.

- `--parallel` runs the suite across multiple worker processes, which is much faster than a serial run.
- `--compact` switches PHPUnit to its compact printer: a short pass/fail summary with only the failing tests, instead of a per-test output stream — cleaner terminal output and far less output to read through.
- One suite run at a time per host: every `artisan test` run wipes and re-migrates the shared testing database, so concurrent runs — including ones launched by parallel agents — corrupt each other.
- MySQL `created_at` has second granularity: in feature tests do not locate a just-created row with `latest()` (it may pick a pre-existing row) — capture the id from the HTTP response (`$response->json('id')`).
- Group related assertions that share the same context and setup into a single test method, rather than creating many small isolated tests. This reduces setup duplication without sacrificing coverage.
- Organize test files by domain or source module following the repository's existing convention. Name test files `{ClassName}Test.php` — behavior details belong in method names, not file names.
