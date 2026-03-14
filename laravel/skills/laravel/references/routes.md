# Routes

## Conventions

- Use resource routes where possible: `Route::resource('posts', PostController::class)`
- Name all routes explicitly
- Group routes with shared middleware
- Use route model binding for entity resolution
- Prefix API routes with `/api`
- Use `Route::apiResource()` for API-only controllers (no create/edit)

## Structure

```php
// Web routes
Route::middleware('auth')->group(function () {
    Route::resource('posts', PostController::class);
});

// API routes
Route::middleware('auth:sanctum')->prefix('api')->group(function () {
    Route::apiResource('posts', PostController::class);
});
```
