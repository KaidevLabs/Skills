---
name: resource-first-controllers
description: Enforces resource-first controller design in Laravel. Apply whenever creating, modifying, or reviewing controllers, defining routes, or planning new features that involve HTTP endpoints.
---

# Resource-First Controller Design

## Core Principle

Every controller is a **resource** until proven otherwise. Before writing any controller, ask: **"Can this be modeled as a resource?"**

## Decision Flow

1. **Identify the noun** (the thing being acted upon). If you can name a resource, use a resource controller.
2. **Map actions to the 7 standard methods**:
   - `index` → list
   - `create` → show creation form
   - `store` → persist new
   - `show` → display one
   - `edit` → show edit form
   - `update` → persist changes
   - `destroy` → delete
3. **Partial resources are encouraged** (1–7 methods). Use `Route::resource(...)->only([...])` or `->except([...])`.
4. **Invokable controllers are a LAST RESORT** — only when the action genuinely cannot map to any standard method (e.g., `GenerateMonthlyReportController`, `ExchangeOAuthTokenController`).

## Hard Rules (do not violate)

- ❌ **No custom methods on resource controllers.** A controller either has only resource methods, or it is invokable.
- ❌ **No mixed controllers.** If you feel tempted to add `publish()`, `archive()`, `approve()` → that's a new resource (`PublishedPostController@store`, `PostArchiveController@store`, etc.).
- ❌ **No grab-bag controllers** (e.g., `UserController` with `profile()`, `settings()`, `avatar()`). Split into `UserProfileController`, `UserSettingsController`, `UserAvatarController`.
- ✅ **Nested resources** are preferred over custom methods: `Route::resource('posts.comments', ...)`.
- ✅ **State changes are resources too**: subscribing = `SubscriptionController@store`, unsubscribing = `SubscriptionController@destroy`.

## Reframing Patterns (use these)

| Tempting custom method    | Resource-first refactor                                       |
| ------------------------- | ------------------------------------------------------------- |
| `PostController@publish`  | `PublishedPostController@store`                               |
| `UserController@activate` | `ActiveUserController@store`                                  |
| `OrderController@cancel`  | `OrderController@destroy` or `CancelledOrderController@store` |
| `AuthController@login`    | `SessionController@store`                                     |
| `AuthController@logout`   | `SessionController@destroy`                                   |
| `UserController@search`   | `UserController@index` (filter via query params)              |

## When Invokable Is Acceptable

Only when ALL of these are true:

- The action does not create/read/update/delete an identifiable resource.
- It cannot be reframed as a state-resource without contortion.
- Examples: webhooks (`StripeWebhookController`), one-off jobs (`RecalculateRankingsController`), pure RPC integrations.

## Required Workflow For The Assistant

When the user asks for any controller or endpoint:

1. **State the resource** you've identified.
2. **List which of the 7 methods** apply, and which are excluded.
3. **Generate the artisan command**: `php artisan make:controller {Name}Controller --resource --model={Model}`.
4. **Generate the route** using `Route::resource(...)` (with `->only(...)` if partial).
5. If proposing an invokable controller, **explicitly justify** why none of the 7 methods fit.

## Self-Check Before Returning Code

- [ ] Does this controller have ONLY resource methods, OR is it `__invoke`?
- [ ] Did I avoid adding `publish/approve/toggle/etc.` as a method?
- [ ] If I used invokable, did I justify why no resource framing works?
- [ ] Are routes declared with `Route::resource` / `Route::apiResource` (not individual `Route::get/post`)?
