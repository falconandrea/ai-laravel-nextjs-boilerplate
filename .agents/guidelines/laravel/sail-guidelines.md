# Laravel Sail Development Guidelines

> These guidelines are specific to Laravel 12 projects using Sail for local development.

---

## 🐳 Sail Command Reference

### Always use Sail commands (NEVER native commands)

```bash
# ✅ CORRECT - Use Sail
./vendor/bin/sail up -d
./vendor/bin/sail artisan migrate
./vendor/bin/sail composer require package/name
./vendor/bin/sail npm install
./vendor/bin/sail test

# ❌ WRONG - Don't use native commands
php artisan migrate           # Won't use Docker environment
composer require package/name # Wrong PHP version
npm install                   # Wrong Node version
```

### Shell Alias (Recommended)

Add to `~/.bashrc` or `~/.zshrc`:
```bash
alias sail='./vendor/bin/sail'
```

Then you can use:
```bash
sail up -d
sail artisan migrate
```

---

## 🚀 Common Sail Commands

### Container Management

```bash
# Start all containers
sail up -d

# Stop all containers
sail down

# Restart containers
sail restart

# View logs
sail logs
sail logs -f  # Follow logs

# Check container status
sail ps
```

### Artisan Commands

```bash
# Run any artisan command
sail artisan [command]

# Examples
sail artisan migrate
sail artisan db:seed
sail artisan make:model Post -mfc
sail artisan queue:work
sail artisan cache:clear
sail artisan config:cache
```

### Composer

```bash
# Install dependencies
sail composer install

# Add package
sail composer require spatie/laravel-permission

# Add dev package
sail composer require --dev laravel/pint

# Update packages
sail composer update

# Dump autoload
sail composer dump-autoload
```

### NPM/Node

```bash
# Install node packages
sail npm install

# Run dev server (Vite)
sail npm run dev

# Build for production
sail npm run build

# Run tests
sail npm run test
```

### Database Access

```bash
# Access MySQL CLI
sail mysql

# Common MySQL commands once inside
mysql> SHOW DATABASES;
mysql> USE your_database;
mysql> SHOW TABLES;
mysql> DESCRIBE users;
mysql> SELECT * FROM users LIMIT 5;
mysql> exit;

# Run raw SQL file
sail mysql < database/scripts/setup.sql
```

### Redis Access

```bash
# Access Redis CLI
sail redis

# Common Redis commands
redis> KEYS *
redis> GET key_name
redis> FLUSHALL  # Clear all data
redis> exit
```

### Testing

```bash
# Run PHPUnit tests
sail test

# Run specific test file
sail test tests/Feature/UserTest.php

# Run Pest tests (if using Pest)
sail test --parallel

# With coverage
sail test --coverage
```

### Shell Access

```bash
# Access container shell
sail shell

# Access as root
sail root-shell

# Run single command
sail shell -c "ls -la"
```

---

## 📁 Project Structure with Sail

### docker-compose.yml

Sail's Docker setup is in `docker-compose.yml`. Common services:

```yaml
services:
  laravel.test:    # Main app container
  mysql:           # Database
  redis:           # Cache/Queue
  meilisearch:     # Search (optional)
  mailpit:         # Email testing
  selenium:        # Browser testing (optional)
```

### .env Configuration

```env
# App
APP_SERVICE=laravel.test
APP_PORT=80  # Change if port conflict

# Database
DB_CONNECTION=mysql
DB_HOST=mysql  # Service name from docker-compose.yml
DB_PORT=3306
DB_DATABASE=your_database
DB_USERNAME=sail
DB_PASSWORD=password

# Redis
REDIS_HOST=redis  # Service name
REDIS_PASSWORD=null
REDIS_PORT=6379

# Mail (Mailpit for local testing)
MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null

# Queue
QUEUE_CONNECTION=redis  # or 'database' for simple projects

# Session
SESSION_DRIVER=redis  # or 'database'
```

---

## 🏗️ Laravel Project Patterns

### Controller Pattern (Thin Controllers)

```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\StorePostRequest;
use App\Http\Resources\PostResource;
use App\Services\PostService;
use Illuminate\Http\JsonResponse;

class PostController extends Controller
{
    public function __construct(
        private PostService $postService
    ) {}

    /**
     * Display a listing of posts
     */
    public function index(): JsonResponse
    {
        $posts = $this->postService->getPaginatedPosts();
        
        return response()->json(
            PostResource::collection($posts)
        );
    }

    /**
     * Store a newly created post
     */
    public function store(StorePostRequest $request): JsonResponse
    {
        $post = $this->postService->createPost(
            $request->validated()
        );
        
        return response()->json(
            new PostResource($post),
            201
        );
    }
}
```

### Service Layer Pattern

```php
<?php

namespace App\Services;

use App\Models\Post;
use App\Models\User;
use Illuminate\Pagination\LengthAwarePaginator;
use Illuminate\Support\Facades\DB;

class PostService
{
    /**
     * Get paginated posts with author
     */
    public function getPaginatedPosts(int $perPage = 15): LengthAwarePaginator
    {
        return Post::with(['author:id,name,email'])
            ->latest()
            ->paginate($perPage);
    }

    /**
     * Create a new post
     */
    public function createPost(array $data): Post
    {
        return DB::transaction(function () use ($data) {
            $post = Post::create([
                'title' => $data['title'],
                'content' => $data['content'],
                'user_id' => auth()->id(),
                'status' => 'draft',
            ]);

            // Additional logic (tags, notifications, etc.)
            $this->attachTags($post, $data['tags'] ?? []);
            $this->notifyFollowers($post);

            return $post->fresh(['author', 'tags']);
        });
    }

    private function attachTags(Post $post, array $tagIds): void
    {
        $post->tags()->sync($tagIds);
    }

    private function notifyFollowers(Post $post): void
    {
        // Notification logic
    }
}
```

### Form Request Validation

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rule;

class StorePostRequest extends FormRequest
{
    /**
     * Determine if user is authorized
     */
    public function authorize(): bool
    {
        return true; // Or check policy
    }

    /**
     * Validation rules
     */
    public function rules(): array
    {
        return [
            'title' => [
                'required',
                'string',
                'max:255',
            ],
            'content' => [
                'required',
                'string',
                'min:100',
            ],
            'status' => [
                'required',
                Rule::in(['draft', 'published']),
            ],
            'tags' => [
                'nullable',
                'array',
            ],
            'tags.*' => [
                'exists:tags,id',
            ],
        ];
    }

    /**
     * Custom error messages
     */
    public function messages(): array
    {
        return [
            'title.required' => 'Please provide a title for your post.',
            'content.min' => 'Post content must be at least 100 characters.',
        ];
    }

    /**
     * Prepare data for validation
     */
    protected function prepareForValidation(): void
    {
        $this->merge([
            'user_id' => auth()->id(),
        ]);
    }
}
```

### API Resource Pattern

```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class PostResource extends JsonResource
{
    /**
     * Transform resource into array
     */
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'title' => $this->title,
            'slug' => $this->slug,
            'excerpt' => $this->excerpt,
            'content' => $this->when(
                $request->routeIs('posts.show'),
                $this->content
            ),
            'status' => $this->status,
            'published_at' => $this->published_at?->toISOString(),
            
            // Relationships
            'author' => new UserResource($this->whenLoaded('author')),
            'tags' => TagResource::collection($this->whenLoaded('tags')),
            'comments_count' => $this->when(
                isset($this->comments_count),
                $this->comments_count
            ),
            
            // Timestamps
            'created_at' => $this->created_at->toISOString(),
            'updated_at' => $this->updated_at->toISOString(),
        ];
    }
}
```

### Model with Relationships

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\BelongsToMany;
use Illuminate\Database\Eloquent\Relations\HasMany;
use Illuminate\Database\Eloquent\SoftDeletes;

class Post extends Model
{
    use HasFactory, SoftDeletes;

    /**
     * Fillable attributes
     */
    protected $fillable = [
        'title',
        'slug',
        'content',
        'excerpt',
        'status',
        'published_at',
        'user_id',
    ];

    /**
     * Attributes to cast
     */
    protected $casts = [
        'published_at' => 'datetime',
    ];

    /**
     * Boot method for model events
     */
    protected static function boot()
    {
        parent::boot();

        static::creating(function ($post) {
            $post->slug = Str::slug($post->title);
        });
    }

    /**
     * Relationships
     */
    public function author(): BelongsTo
    {
        return $this->belongsTo(User::class, 'user_id');
    }

    public function tags(): BelongsToMany
    {
        return $this->belongsToMany(Tag::class)
            ->withTimestamps();
    }

    public function comments(): HasMany
    {
        return $this->hasMany(Comment::class);
    }

    /**
     * Scopes
     */
    public function scopePublished($query)
    {
        return $query->where('status', 'published')
            ->whereNotNull('published_at')
            ->where('published_at', '<=', now());
    }

    public function scopeByAuthor($query, User $user)
    {
        return $query->where('user_id', $user->id);
    }
}
```

---

## 🧪 Testing with Sail

### Feature Test Example

```php
<?php

namespace Tests\Feature;

use App\Models\Post;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class PostTest extends TestCase
{
    use RefreshDatabase;

    public function test_user_can_create_post(): void
    {
        $user = User::factory()->create();

        $response = $this->actingAs($user)
            ->postJson('/api/posts', [
                'title' => 'Test Post',
                'content' => 'This is test content that is long enough to pass validation.',
                'status' => 'draft',
            ]);

        $response->assertStatus(201)
            ->assertJsonStructure([
                'id',
                'title',
                'content',
                'author' => ['id', 'name'],
            ]);

        $this->assertDatabaseHas('posts', [
            'title' => 'Test Post',
            'user_id' => $user->id,
        ]);
    }

    public function test_validation_fails_for_short_content(): void
    {
        $user = User::factory()->create();

        $response = $this->actingAs($user)
            ->postJson('/api/posts', [
                'title' => 'Test',
                'content' => 'Too short',
                'status' => 'draft',
            ]);

        $response->assertStatus(422)
            ->assertJsonValidationErrors(['content']);
    }
}
```

Run tests:
```bash
sail test
sail test --filter PostTest
```

---

## ⚠️ Common Pitfalls

### ❌ DON'T: Use native commands
```bash
php artisan migrate  # Wrong!
```

### ✅ DO: Use Sail
```bash
sail artisan migrate  # Correct!
```

### ❌ DON'T: Forget to eager load
```php
$posts = Post::all();
foreach ($posts as $post) {
    echo $post->author->name;  // N+1 query problem!
}
```

### ✅ DO: Eager load relationships
```php
$posts = Post::with('author')->get();
foreach ($posts as $post) {
    echo $post->author->name;  // No extra queries!
}
```

### ❌ DON'T: Put logic in controllers
```php
public function store(Request $request)
{
    // 50 lines of business logic...
}
```

### ✅ DO: Use service layer
```php
public function store(StorePostRequest $request)
{
    $post = $this->postService->createPost($request->validated());
    return response()->json($post, 201);
}
```

---

## 📚 Quick Reference

**Start project**: `sail up -d`
**Stop project**: `sail down`
**Run migration**: `sail artisan migrate`
**Access DB**: `sail mysql`
**Run tests**: `sail test`
**Clear cache**: `sail artisan cache:clear`
**View logs**: `sail logs -f`

---

For more Sail documentation: https://laravel.com/docs/12.x/sail
