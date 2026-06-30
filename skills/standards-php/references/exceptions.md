# Exceptions

## Rules

- Extend one of: `\RuntimeException`, `\DomainException`, `\InvalidArgumentException`
- Custom exceptions for application-specific error cases
- Include context via constructor parameters (not getters that compute)
- HTTP exceptions use Laravel's `Symfony\Component\HttpKernel\Exception\HttpException` or custom renderable exceptions
- Store in `App\Exceptions\`

## Template

```php
declare(strict_types=1);

namespace App\Exceptions;

class PostNotFoundException extends \RuntimeException
{
    public function __construct(
        public readonly string $postId,
        string $message = null,
        int $code = 404,
        ?\Throwable $previous = null,
    ) {
        parent::__construct(
            $message ?? "Post not found: {$postId}",
            $code,
            $previous,
        );
    }
}
```

## HTTP exception rendering

```php
use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;

throw new NotFoundHttpException('Post not found.');
```

## When to create a custom exception

- The exception carries context the handler needs (entity ID, failed validation result)
- The exception needs special rendering (custom HTTP status, structured error response)
- The exception is caught and handled differently from generic exceptions
