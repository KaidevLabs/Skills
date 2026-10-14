# Coding Style Guidelines

- PRIORITY: Pragmatic and readable code. Prioritize clarity and maintenance over theoretical decoupling.

- RIGOR: Before reporting any task as complete, verify thoroughness: review all related files (not just the ones mentioned), confirm nothing was missed, and cross-check against sibling files and existing patterns. Do not rely on assumptions from earlier analysis — re-verify. When applying a fix pattern across multiple instances, ensure every dimension of the fix (e.g., type cast, default parameter, argument order) is applied consistently to all instances in the same pass — do not fix partially.

- PHP IMPORTS:
  - NEVER use fully qualified class names (e.g., `\Carbon\Carbon`) inline within the code.
  - ALWAYS import classes using `use` statements at the top of the file.
  - Exception: only use FQCN if there is a genuine class name collision that cannot be resolved with an alias.

- ARCHITECTURE:
  - Favor straightforward implementation.
  - Use Classes and Interfaces when they provide genuine organization, but avoid "Architecture Astronaut" patterns (deep service wrappers, unnecessary abstraction layers, over-engineered design patterns) that obscure the business logic.
  - Controllers should primarily orchestrate requests and responses rather than contain complex business logic.
  - If a simple, self-contained logic block inside a controller or a model method suffices, prefer that over creating additional classes or indirection.
  - When logic grows in complexity, becomes difficult to read, or is reused in multiple places, extract it into a dedicated class.

- MODEL STRUCTURE: Follow the standard Laravel community ordering conventions (traits, properties, relationships, scopes, accessors, then custom methods). Follow the repository's established casts pattern (property `protected $casts = [...]` vs `casts()` method) consistently.

- SENIOR STANDARDS:
  - ELOQUENT: Use Query Scopes for reusable or complex filtering logic. Avoid long or repeated `where` chains in Controllers.
  - PERFORMANCE: Be proactive in preventing N+1 queries. Always suggest eager loading (`with()`) when relationships are accessed.
  - CLEAN CODE: Use early returns to reduce nesting levels.
  - SECURITY: Ensure all mass-assignment is protected via Form Requests or `$fillable`.

- LARAVEL:
  - Use idiomatic Laravel patterns.
  - Lean into the framework's native capabilities (Eloquent models, query scopes, collections, policies, jobs, events, service providers, and built-in helpers) before building custom abstractions.
  - Prefer Form Request classes for non-trivial validation logic instead of placing rules directly in controllers.
  - Prefer Laravel Collections (`map`, `filter`, `pluck`, etc.) over manual loops when they improve readability.

- CONFIG: Never pass a default value to `config()`. Config values must always be explicitly defined in the config files: use `config('key')`, not `config('key', 'default')`.

- LOGGING:
  - Use `logger()->error()` for logging exceptions:

    ```php
    try {
        // code
    } catch (\Exception $e) {
        logger()->error('Error: ' . $e->getMessage(), [
            'exception' => $e,
        ]);
    }
    ```
  - Avoid `Log::error()` (facade import) and `report()` unless the repository already uses them.

- TESTING:
  - Choose the appropriate test type: Unit for isolated logic, Feature for HTTP/integration, browser tests for real browser flows.
  - Every class containing business logic (Policies, Observers, Actions, Controllers, Form Requests, Jobs, etc.) must have its own dedicated test. Before finalizing a feature branch, verify 1:1 coverage: list all created/modified source classes and confirm each testable class has a corresponding test file.
  - Tests should be deterministic, validate observable behavior, and be focused on a single behavior.
  - Test names should clearly describe the behavior being validated.
  - Apply professional testing best practices by default (AAA pattern, test-owned expected values, no tautological assertions, no test logic such as conditionals or loops, single reason to fail, etc.) without requiring explicit instruction.
  - Use blank lines to visually separate distinct setup concerns within a test (e.g., authentication context vs. data creation) and between Arrange/Act/Assert phases.
  - Faker: always use the global helper `fake()` instead of `$this->faker`.
  - Prefer value assertions (`assertEquals`, `assertSame`, `assertTrue`) over type assertions (`assertInstanceOf`, `assertIsBool`) when the test provides known input. A value assertion proves both correctness and type simultaneously, making type-only checks redundant. Example: use `assertEquals('1990-05-15', $model->date->format('Y-m-d'))` instead of `assertInstanceOf(Carbon::class, $model->date)`.
  - Ensure substring assertions (`assertStringContainsString`) use substrings specific enough to avoid false positives. If the domain has related terms (e.g., "Active" / "Inactive"), the asserted substring must not accidentally match the wrong variant.
  - Policy tests must assert every distinct ability the policy defines (viewAny, view, create, update, delete, restore where defined, replicate, plus any custom abilities), not just a representative subset. Include both class-level and instance-level abilities.
  - Policy tests must cover every application role the app defines, plus a user without any role, not just a generic "non-admin" user — use the seeded users with real roles so the test detects accidental permission grants to the wrong role.
  - Tests that evaluate policies depending on Gates (e.g., `Gate::allows('admin')`) must call `$this->actingAs($user)` before asserting permissions, since the Gate resolves the authenticated user, not the `$user` parameter.
  - A test method reuses one app instance for every HTTP call, and the auth layer caches the principal resolved from the first token — when a method makes multiple requests with different tokens, call `$this->app['auth']->forgetGuards()` between them.
  - MySQL `created_at` has second granularity: in feature tests do not locate a just-created row with `latest()` (it may pick a pre-existing row) — capture the id from the HTTP response (`$response->json('id')`).
  - Model tests must cover not only relationships and casts, but also capabilities granted by interfaces and traits (e.g., `SoftDeletes`, `Searchable`, media traits). Inspect the model's `implements` clause and `use` statements to identify testable behavior beyond column-level concerns.
  - Organize test files by domain or source module following the repository's existing convention. Name test files `{ClassName}Test.php` — behavior details belong in method names, not file names.

- ABSTRACTIONS:
  - Introduce abstractions only when they clearly improve readability, reuse, or separation of concerns.
  - Avoid creating interfaces solely for "future-proofing" when there is only one implementation.
  - Interfaces are appropriate when representing external dependencies (e.g., APIs, payment providers, infrastructure integrations).

- COMPLEXITY:
  - Prefer small, cohesive classes with a clear responsibility.
  - If a class grows significantly in size or starts handling multiple concerns, consider extracting collaborators.

- METHOD CHAINING: Keep the first method call on the same line as the variable (`$model->first()`, not `$model\n    ->first()`). Subsequent chained calls go on indented lines.

- AVOID:
  - Do not wrap native Laravel functionality in generic "Manager", "Service", or "Wrapper" classes that only proxy framework calls.
  - Avoid abstraction layers that do not add meaningful behavior or clarity to the code.
  - Avoid vague class names such as `Helper`, `Utils`, or `Manager` when they do not clearly describe business intent.
  - Avoid repository layers that simply proxy Eloquent models without adding domain logic.
  - Avoid accessing relationships inside loops without eager loading (`with()`).
  - Avoid using browser tests for logic that can be validated with Unit or Feature tests.
