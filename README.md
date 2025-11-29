# This are my Laravel basics and must configuration for framework files and tooling

## Install laravel and php tools

Larastan 
```
composer require "larastan/larastan" --dev
```

Rector 
```
composer require rector/rector --dev
```

Rector for laravel
```
composer require --dev driftingly/rector-laravel
```

## AppServiceProvider.php
```php
<?php

declare(strict_types=1);

namespace App\Providers;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Vite;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     */
    public function register(): void
    {
        $this->configureModels();
        $this->configureVite();
        $this->configureCommands();
    }

    /**
     * Configure the application's commands.
     */
    private function configureCommands(): void
    {
        DB::prohibitDestructiveCommands(
            $this->app->environment('production'),
        );
    }

    private function configureModels(): void
    {
        Model::shouldBeStrict(! $this->app->environment('production'));
    }

    private function configureVite(): void
    {
        Vite::useAggressivePrefetching();
    }

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        //
    }
}
```

## Laravel Pint
```json
{
  "preset": "laravel",
  "rules": {
    "declare_strict_types": true,
    "fully_qualified_strict_types": true,
    "global_namespace_import": {
      "import_classes": true,
      "import_constants": true,
      "import_functions": true
    },
    "mb_str_functions": true,
    "modernize_types_casting": true,
    "new_with_parentheses": false,
    "no_superfluous_elseif": true,
    "no_useless_else": true,
    "no_multiple_statements_per_line": true,
    "lowercase_keywords": true,
    "lowercase_static_reference": true,
    "array_push": true,
    "strict_comparison": true,
    "backtick_to_shell_exec": true
  }
```

## Larastan
```neon
includes:
    - vendor/larastan/larastan/extension.neon
    - vendor/phpstan/phpstan/conf/bleedingEdge.neon

parameters:
    level: 6

    paths:
        - app/
```

## Rector
```php
<?php

declare(strict_types=1);

use Rector\CodingStyle\Rector\ClassLike\NewlineBetweenClassLikeStmtsRector;
use Rector\Config\RectorConfig;
use RectorLaravel\Rector\FuncCall\RemoveDumpDataDeadCodeRector;
use RectorLaravel\Set\LaravelSetList;

return RectorConfig::configure()
    ->withPaths([
        __DIR__.'/app',
        __DIR__.'/bootstrap/app.php',
        __DIR__.'/database',
        __DIR__.'/public',
    ])
    ->withSets([
        LaravelSetList::LARAVEL_CODE_QUALITY,
        LaravelSetList::LARAVEL_COLLECTION,
        LaravelSetList::LARAVEL_FACTORIES,
        LaravelSetList::LARAVEL_IF_HELPERS,
        LaravelSetList::LARAVEL_TYPE_DECLARATIONS,
    ])
    ->withPreparedSets(
        deadCode: true,
        codeQuality: true,
        codingStyle: true,
        typeDeclarations: true,
        privatization: true,
        earlyReturn: true,
    )
    ->withConfiguredRule(RemoveDumpDataDeadCodeRector::class, [
        'dd',
        'dump',
        'var_dump',
    ])
    ->withSkip([
        NewlineBetweenClassLikeStmtsRector::class,
    ]);
```

## Commands for composer.json
```json
"lint": [
      "pint",
      "npm run lint"
    ],
    "refactor": [
      "rector"
    ],
    "test:unit": [
      "pest --parallel"
    ],
    "test:types": [
      "phpstan analyse"
    ],
    "test:lint": [
      "pint --test",
      "npm run test:lint"
    ],
    "test:refactor": [
      "rector --dry-run"
    ],
    "test": [
      "@test:lint",
      "@test:unit",
      "@test:types",
      "@test:refactor"
    ]
```

## Commands for package.json (only if using inertia.js)
```json
"scripts": {
    "build": "vite build",
    "build:ssr": "vite build && vite build --ssr",
    "dev": "vite",
    "format": "prettier --write resources/",
    "format:check": "prettier --check resources/",
    "lint": "prettier --write resources/ && eslint . --fix",
    "test:lint": "prettier --check resources/ && eslint ."
  },
```
## Packages for blade files
```
npm install -D prettier prettier-plugin-tailwindcss
```

```
npm install --save-dev @shufo/prettier-plugin-blade prettier
```

## Commands for package.json (only if using blade files)
```json
"scripts": {
    "build": "vite build",
    "dev": "vite",
    "format": "prettier --write resources/views/**/*.blade.php resources/views/**/**/*.blade.php",
    "format:check": "prettier --check resources/views/**/*.blade.php resources/views/**/**/*.blade.php"
},

