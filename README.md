# Laravel Database Schema Builder

<p>This repository provides a simple mechanism for building database schemas with Laravel using a SchemaBuilder class and a HasAuditableFields trait that provides concept of Laravel Code Reusability and Abstraction Using Trait and method invocation .</p>


## Usage

### 1. Create SchemaBuilder Class

<p>Create a `SchemaBuilder` class in the `app/Support` directory. This class contains methods for adding common fields to database tables.</p>

```php

<?php

namespace App\Support;

use Illuminate\Database\Schema\Blueprint;

class SchemaBuilder
{
    public function auditable(Blueprint $table)
    {
        $table->unsignedBigInteger('created_by')->nullable();
        $table->unsignedBigInteger('updated_by')->nullable();
        $table->unsignedBigInteger('deleted_by')->nullable();
        $table->timestamp('deleted_at')->nullable();
        $table->integer('status')->default(0);
    }
}

```

### Create Trait
<p>Create a HasAuditableFields trait in the app/Traits directory. This trait provides a method for applying auditable fields to database tables.</p>


```php

<?php

namespace App\Traits;
use Illuminate\Database\Schema\Blueprint;
use App\Support\SchemaBuilder;

trait HasAuditableFields
{
    public static function auditable(Blueprint $table)
    {
        (new SchemaBuilder)->auditable($table);
    }
}


```

### Use the Trait in migration:

```php

<?php

use App\Traits\HasAuditableFields;
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{

    use HasAuditableFields;
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('roles', function (Blueprint $table) {
            $table->id();
            $table->string('name')->unique();
            self::auditable($table);
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('roles');
    }
};

```


### Migrate

```bash
php artisan migrate:fresh
```
