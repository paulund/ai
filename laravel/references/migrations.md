# Migrations

## Conventions

- One migration per schema change
- Always add `resource_id` (UUID) column for public-facing models
- Index foreign keys and frequently queried columns
- Use `foreignId()->constrained()->cascadeOnDelete()` for relationships
- Never modify existing migrations — create new ones

```php
return new class extends Migration
{
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->uuid('resource_id')->unique();
            $table->foreignId('user_id')->constrained()->cascadeOnDelete();
            $table->string('title');
            $table->text('content');
            $table->timestamp('published_at')->nullable();
            $table->timestamps();

            $table->index('published_at');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```
