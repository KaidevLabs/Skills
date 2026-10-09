---
name: resource-first-controllers
description: Design Laravel HTTP controllers around resource actions and explicit routing. Use when adding or refactoring Laravel controllers, routes, or CRUD workflows; not applicable to non-Laravel frameworks.
---

# Resource-first controllers (Laravel)

Apply this skill only to Laravel applications. Start from the resource and its lifecycle, then use Laravel's conventional resource actions and routes. Follow the consumer repository's Laravel version, architecture, authorization, response, and testing conventions.

## Prefer conventional resource actions

Use the seven conventional actions when they match the HTTP contract:

| Action | Typical purpose |
| --- | --- |
| index | List or search resources |
| create | Show a creation form when the application has server-rendered forms |
| store | Validate and create a resource |
| show | Display one resource |
| edit | Show an edit form when applicable |
| update | Validate and update a resource |
| destroy | Delete or archive a resource |

Declare routes with Route::resource or Route::apiResource when their generated contract fits. Limit generated actions with only or except when necessary. Match the project's established route naming, model binding, authorization, and response patterns.

## Handle non-CRUD behavior deliberately

Before adding a custom controller method, ask whether the operation is a resource lifecycle action, a separate resource, or a focused application action. Prefer a named route and dedicated invokable controller for a single distinct action, or a separate resource/controller when the operation has its own lifecycle. Avoid action-heavy controllers, ambiguous verbs, and methods that combine unrelated responsibilities.

Do not force the seven-action pattern when it obscures a clear domain action or conflicts with an established API contract. Keep validation, authorization, persistence, and response responsibilities aligned with the application's existing boundaries; the controller should coordinate rather than become a repository-specific grab bag.

## Before changing routes

- Inspect route registration, route names, middleware, implicit bindings, and callers.
- Preserve externally consumed URLs and response shapes unless the specification explicitly changes them.
- Check for route collisions and authorization coverage.
- Use the application's documented test and formatting commands. Do not assume a particular project path, shell wrapper, database, or test framework.
