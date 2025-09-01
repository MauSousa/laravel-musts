# This are my Laravel basics and must configuration for framework files and tooling

## Install laravel and php tools

Larastan 
```
composer require --dev "larastan/larastan"
```

Rector 
```
composer require rector/rector --dev
```

## AppServiceProvider.php
``` php
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
```

## Laravel Pint
```php
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
}
```

## Larastan
```php
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

use Rector\Config\RectorConfig;

return RectorConfig::configure()
    ->withPaths([
        __DIR__ . '/app',
        __DIR__ . '/bootstrap/app.php',
        __DIR__ . '/database',
        __DIR__ . '/public',
    ])
    ->withPreparedSets(
        deadCode: true,
        codeQuality: true,
        typeDeclarations: true,
        privatization: true,
        earlyReturn: true,
        strictBooleans: true,
    )
    ->withPhpSets();
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
