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

# Setting up a Laravel Project
Command line: 
