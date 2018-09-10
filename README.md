# Master Lorence
## Command line
> - **Getting Started**
>> - [Setting up a Laravel Project](#setting-up-a-laravel-project)
>> - [Setting up a Laravel Project](#setting-up-a-laravel-project)

    php --version 
    composer
    composer create-project laravel/laravel getting-started
    Alt + F12 (PHP Storm)
    php artisan serve
    
    Shiftx2 web.php
    Shiftx2 welcome.blade
    
    php artisan migrate
    php artisan migrate:refresh
    
    php artisan make:controller PhotoController
    
    php artisan route:list
    php artisan make:auth
    
    php artisan migrate:rollback --step=1
    
    php artisan make:migration create_permissions_table --create=permissions
    
    truncate -s 0 /app/storage/logs/laravel.log
    echo "" > storage/logs/laravel.log
    
    DEBUG:
    Log::info('AdminLoginController', ['request' => $request->all()]);
    Log::info('AdminLoginController', ['result' => 'login']);
    dd( Auth::guard('admin')->attempt(['email' => $request->email, 'password' => $request->password]));
    
    
    DB
    try {
            DB::connection()->getPdo();
            dd("Successfully");
        } catch (\Exception $e) {
            dd("Could not connect to the database.  Please check your configuration.");
        }
        
    DB: Admin
    dd(Admin::all());
    
    CREATE PASSWORK: 
    $temp = Hash::make("Abc@123456");
    dd($temp);
    
    Remove register route from Auth::routes
    Illuminate/Routing/Router.php
    
    composer install [**failed to open stream: No such file or directory laravel**]
    
#DataTables
    
    composer require yajra/laravel-datatables-oracle:^8.0
    composer require yajra/laravel-datatables:^1.0
    
    Yajra\DataTables\DataTablesServiceProvider::class,DataTables warning: table id=incenseTable - Requested unknown
    php artisan vendor:publish --tag=datatables
    
    ALTER TABLE tablename AUTO_INCREMENT = 1

# Setting up a Laravel Project
Command line: 

# Load header and footer from Module in Laravel
<h4 name="7e0d" id="7e0d" class="graf graf--h4 graf-after--p">Step 1: Create new project and install&nbsp;jwt-auth</h4>
<strong class="markup--strong markup--p-strong">Create Laravel project</strong>
<strong class="markup--strong markup--p-strong">Install jwt-auth</strong>
- Open composer.json and update the require object to include jwt-auth

```php
"require": {
    "php": ">=7.1.3",
    "fideloper/proxy": "~4.0",
    "laravel/framework": "5.5.*",
    "laravel/tinker": "~1.0",
    "tymon/jwt-auth": "1.0.0-rc.1"
}
```

<h4 name="f916" id="f916" class="graf graf--h4 graf-after--pre">Step 2: Add JWT Provider and&nbsp;Facades</h4>
- Open up config/app.php, find the providers array and add the jwt-auth provider:

```php
Tymon\JWTAuth\Providers\LaravelServiceProvider::class,
```

- Find the aliases array and add the jwt-auth facades:

```php
'JWTAuth' => Tymon\JWTAuth\Facades\JWTAuth::class, 
'JWTFactory' => Tymon\JWTAuth\Facades\JWTFactory::class
```

- We also need to publish the assets for this package. From the command line:

```php
php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"
```

- After you run this command you will see a new file in the config folder called jwt.php. This file contains settings for jwt-auth, one of which we need to change right away. We need to generate a secret key, from the command line, run:

```php
php artisan jwt:secret
```

- Register the jwt.auth and jwt.refresh middleware in app/http/Kernel.php

```php
protected $routeMiddleware = [
...
    'jwt.auth' => 'Tymon\JWTAuth\Middleware\GetUserFromToken',
    'jwt.refresh' => 'Tymon\JWTAuth\Middleware\RefreshToken',
];

    protected $routeMiddleware = [
        'auth' => \Illuminate\Auth\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
        'jwt.auth' => 'Tymon\JWTAuth\Middleware\GetUserFromToken',
        'jwt.refresh' => 'Tymon\JWTAuth\Middleware\RefreshToken',
    ];
}

```

<h4 name="5ea1" id="5ea1" class="graf graf--h4 graf-after--figure">Step 3: Set Up Routes&nbsp;(*)</h4>
<strong class="markup--strong markup--p-strong">routes/api.php</strong>




