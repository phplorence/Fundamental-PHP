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
    
    truncate -s 0 /app/storage/logs/laravel.log
    
    
    DEBUG:
    Log::info('AdminLoginController', ['request' => $request->all()]);
    Log::info('AdminLoginController', ['result' => 'login']);
    dd( Auth::guard('admin')->attempt(['email' => $request->email, 'password' => $request->password]));

# Setting up a Laravel Project
Command line: 
