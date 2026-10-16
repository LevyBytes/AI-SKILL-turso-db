# Laravel migrations and eloquent inserts

[Context index](kotlin-and-php-sdk-workflows.md) · [Setup and preceding context](laravel-installation-and-local-configuration.md) · [Previous phase](laravel-remote-replicas-and-migration-schema.md)

### Step 5: Run migrations

Run the migration to create the users table:

```
php artisan migrate

```

### Step 6: Execute CRUD operations

After that, use Eloquent to perform CRUD operations:

```
use App\Models\User;

$user = User::create([
    'name' => 'John Doe',
    'email' => 'john@example.com',
]);

```
