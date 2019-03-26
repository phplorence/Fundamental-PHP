# Master Lorence
## Command line
> - **Getting Started**
>> - [Setting up a Laravel Project](#setting-up-a-laravel-project)
>> - [Setting up a Laravel Project](#setting-up-a-laravel-project)

    php --version 
    
    sudo kill `sudo lsof -t -i:8000`
    
    composer
    composer create-project laravel/laravel getting-started
    
    php artisan key:generate
    
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

```php
Route::middleware('auth:api')->get('/user', function (Request $request) {
    return $request->user();
});

Route::post('register', 'AuthController@register');
Route::post('login', 'AuthController@login');
Route::post('recover', 'AuthController@recover');

Route::group(['middleware' => ['jwt.auth']], function() {
    Route::get('logout', 'AuthController@logout');
    Route::get('test', function(){
        return response()->json(['foo'=>'bar']);
    });
});
```

- Open up routes/web.php and add the route for verifying.

```php
Route::get('/', function () {
    return view('welcome');
});

Auth::routes();

Route::get('/home', 'HomeController@index')->name('home');

Route::get('user/verify/{verification_code}', 'AuthController@verifyUser');

Route::get('password/reset/{token}', 'Auth\ResetPasswordController@showResetForm')->name('password.request');

Route::post('password/reset', 'Auth\ResetPasswordController@postReset')->name('password.reset');

```
<h4 name="dd32" id="dd32" class="graf graf--h4 graf-after--figure">Step 4: Set Up Database&nbsp;(*)</h4>
- Since we are going to allow users to create their accounts within the application, we will need a table to store all of our users. Thankfully, Laravel already ships with a migration to create a basic users table, so we do not need to manually generate one. The default migration for the users table is located in the database/migrations directory.

- We need to create a new table and add an extra column to the users table. Firstly, we need a boolean field ‘is_verified’ to keep track of whether a user has verified their email address, this will be set to false by default.

- Create a new table “user_verifications” this table will store the user’s verification code. When the user registers, a verification code is generated and stored in the table and an email with a verification link is sent.

- When a user follows this link, we take the passed in verification code and search for it within the user_verifications table. If a matching verified code is found we set the is_verified field for this user to true.

```php
php artisan make:migration create_user_verifications_table
```

<strong class="markup--strong markup--p-strong">The new migration file is created in the database/migrations</strong>

```php
class CreateUserVerificationsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('user_verifications', function (Blueprint $table) {
            $table->increments('id');
            $table->timestamps();
            $table->integer('user_id')->unsigned();
            $table->string('token');
            $table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');
        });

        Schema::table('users', function (Blueprint $table) {
            $table->boolean('is_verified')->default(0);
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('user_verifications');

        Schema::table('users', function (Blueprint $table) {
            $table->dropColumn('is_verified');
        });
    }
}
```

<p name="e34f" id="e34f" class="graf graf--p graf-after--figure"><strong class="markup--strong markup--p-strong">Update </strong><em class="markup--em markup--p-em">app/User.php</em></p>
```php
class User extends Authenticatable
{
    use Notifiable;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name', 'email', 'password',
    ];

    /**
     * The attributes that should be hidden for arrays.
     *
     * @var array
     */
    protected $hidden = [
        'password', 'remember_token',
    ];
}
```

```php
class User extends Authenticatable implements JWTSubject
{
    use Notifiable;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name', 'email', 'password', 'is_verified'
    ];

    /**
     * Get the identifier that will be stored in the subject claim of the JWT.
     *
     * @return mixed
     */
    public function getJWTIdentifier()
    {
        return $this->getKey();
    }

    /**
     * Return a key value array, containing any custom claims to be added to the JWT.
     *
     * @return array
     */
    public function getJWTCustomClaims()
    {
        return [];
    }

    /**
     * The attributes that should be hidden for arrays.
     *
     * @var array
     */
    protected $hidden = [
        'password', 'remember_token',
    ];
}
```

<p name="b851" id="b851" class="graf graf--p graf-after--figure"><strong class="markup--strong markup--p-strong">Update&nbsp;.env file</strong></p>
- Next, we need to update our database and mail settings, update the .env file

- <p name="d5fa" id="d5fa" class="graf graf--p graf-after--figure">And run migration</p>

```php
php artisan migrate
```

- if you get the error below
- Open up Providers/AppServiceProvider.php and update the boot function

```php
<?php
use Illuminate\Support\Facades\Schema;
...
  
  public function boot()
  {
    Schema::defaultStringLength(191);
  }
```

<h4 name="bdcd" id="bdcd" class="graf graf--h4 graf-after--figure">Step 5: Register and Verify Email Address&nbsp;(*)</h4>
- Create a new controller

```php
php artisan make:controller AuthController
```

- This will create AuthController.php in the app/Http/Controllers directory, paste the code below.

```php

<?php

namespace App\Http\Controllers;
use App\User;
use DB;
use Hash;
use Illuminate\Http\Request;
use JWTAuth;
use Mail;
use Validator;

class AuthController extends Controller
{
    /**
     * API Register
     *
     * @param Request $request
     * @return \Illuminate\Http\JsonResponse
     */
    public function register(Request $request)
    {
        $credentials = $request->only('name', 'email', 'password');

        $rules = [
            'name' => 'required|max:255',
            'email' => 'required|email|max:255|unique:users'
        ];

        $validator = Validator::make($credentials, $rules);

        if($validator->fails()) {
            return response()->json(['success'=> false, 'error'=> $validator->messages()]);
        }

        $name = $request->name;
        $email = $request->email;
        $password = $request->password;

        $user = User::create(['name' => $name, 'email' => $email, 'password' => Hash::make($password)]);
        $verification_code = str_random(30); //Generate verification code

        DB::table('user_verifications')->insert(['user_id'=>$user->id,'token'=>$verification_code]);

        $subject = "Please verify your email address.";
        Mail::send('email.verify', ['name' => $name, 'verification_code' => $verification_code],
            function($mail) use ($email, $name, $subject){
                $mail->from(getenv('FROM_EMAIL_ADDRESS'), "From User/Company Name Goes Here");
                $mail->to($email, $name);
                $mail->subject($subject);
            });
        return response()->json(['success'=> true, 'message'=> 'Thanks for signing up! Please check your email to complete your registration.']);
    }
}
```

<strong class="markup--strong markup--p-strong">Verify Email Address</strong>
- In the resources/views directory, create a new directory called email. Then create the file verify.blade.php. Populate the file with the code below.

```php
<!DOCTYPE html>
<html lang="en-US">
<head>
    <meta charset="utf-8">
</head>
<body>

<div>
    Hi {{ $name }},
    <br>
    Thank you for creating an account with us. Don't forget to complete your registration!
    <br>
    Please click on the link below or copy it into the address bar of your browser to confirm your email address:
    <br>

    <a href="{{ url('user/verify', $verification_code)}}">Confirm my email address </a>

    <br/>
</div>

</body>
</html>
```

<p name="c44c" id="c44c" class="graf graf--p graf-after--figure">Add the verifyUser function to <strong class="markup--strong markup--p-strong">AuthController.php</strong></p>

```php
    /**
     * API Verify User
     *
     * @param Request $request
     * @return \Illuminate\Http\JsonResponse
     */
    public function verifyUser($verification_code)
    {
        $check = DB::table('user_verifications')->where('token',$verification_code)->first();
        if(!is_null($check)){
            $user = User::find($check->user_id);
            if($user->is_verified == 1){
                return response()->json([
                    'success'=> true,
                    'message'=> 'Account already verified..'
                ]);
            }
            $user->update(['is_verified' => 1]);
            DB::table('user_verifications')->where('token',$verification_code)->delete();
            return response()->json([
                'success'=> true,
                'message'=> 'You have successfully verified your email address.'
            ]);
        }
        return response()->json(['success'=> false, 'error'=> "Verification code is invalid."]);
    }
```

<h4 name="1221" id="1221" class="graf graf--h4 graf-after--figure">Step 6: Log User In and Out&nbsp;(*)</h4>

```php
    /**
     * API Login, on success return JWT Auth token
     *
     * @param Request $request
     * @return \Illuminate\Http\JsonResponse
     */
    public function login(Request $request)
    {
        $rules = [
            'email' => 'required|email',
            'password' => 'required',
        ];
        $input = $request->only('email', 'password');
        $validator = Validator::make($input, $rules);
        if($validator->fails()) {
            $error = $validator->messages()->toJson();
            return response()->json(['success'=> false, 'error'=> $error]);
        }
        $credentials = [
            'email' => $request->email,
            'password' => $request->password,
            'is_verified' => 1
        ];
        try {
            // attempt to verify the credentials and create a token for the user
            if (! $token = JWTAuth::attempt($credentials)) {
                return response()->json(['success' => false, 'error' => 'Invalid Credentials. Please make sure you entered the right information and you have verified your email address.'], 401);
            }
        } catch (JWTException $e) {
            // something went wrong whilst attempting to encode the token
            return response()->json(['success' => false, 'error' => 'could_not_create_token'], 500);
        }
        // all good so return the token
        return response()->json(['success' => true, 'data'=> [ 'token' => $token ]]);
    }

    /**
     * Log out
     * Invalidate the token, so user cannot use it anymore
     * They have to relogin to get a new token
     *
     * @param Request $request
     * @return \Illuminate\Http\JsonResponse
     */
    public function logout(Request $request) {
        $this->validate($request, ['token' => 'required']);
        try {
            JWTAuth::invalidate($request->input('token'));
            return response()->json(['success' => true]);
        } catch (JWTException $e) {
            // something went wrong whilst attempting to encode the token
            return response()->json(['success' => false, 'error' => 'Failed to logout, please try again.'], 500);
        }
    }
```

<h4 name="7ead" id="7ead" class="graf graf--h4 graf-after--figure">Step 7: Recover&nbsp;Password</h4>

```php
/**
     * API Recover Password
     *
     * @param Request $request
     * @return \Illuminate\Http\JsonResponse
     */
    public function recover(Request $request)
    {
        $user = User::where('email', $request->email)->first();
        if (!$user) {
            $error_message = "Your email address was not found.";
            return response()->json(['success' => false, 'error' => ['email'=> $error_message]], 401);
        }
        try {
            Password::sendResetLink($request->only('email'), function (Message $message) {
                $message->subject('Your Password Reset Link');
            });
        } catch (\Exception $e) {
            //Return with error
            $error_message = $e->getMessage();
            return response()->json(['success' => false, 'error' => $error_message], 401);
        }
        return response()->json([
            'success' => true, 'data'=> ['msg'=> 'A reset email has been sent! Please check your email.']
        ]);
    }
```

<h4 name="9ddc" id="9ddc" class="graf graf--h4 graf-after--figure">Step 8: Testing&nbsp;(*)</h4>
- Try accessing test route without token [GET]
- http://127.0.0.1:8000/api/test
-- Browser shows error: Symfony \ Component \ HttpKernel \ Exception \ UnauthorizedHttpException
Token not provided
-- Postman shows error: 

- Access this link http://127.0.0.1:8000/register in browser to create a new user with Laravel
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/1.png)

- Observe login.blade.php, you will understand default is "login" and now we will change "api/login".
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/2.png)
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/3.png)
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/4.png)

- Analyze code below

```php
public function register(Request $request)
    {
        $credentials = $request->only('name', 'email', 'password');

        $rules = [
            'name' => 'required|max:255',
            'email' => 'required|email|max:255|unique:users'
        ];

        $validator = Validator::make($credentials, $rules);

        if ($validator->fails()) {
            return response()->json(['success' => false, 'error' => $validator->messages()]);
        }

        $name = $request->name;
        $email = $request->email;
        $password = $request->password;

        $user = User::create(['name' => $name, 'email' => $email, 'password' => Hash::make($password)]);
        $verification_code = str_random(30);

        DB::table('user_verifications')->insert(['user_id' => $user->id, 'token' => $verification_code]);

        $subject = "Please verify your email address.";
        Mail::send('email.verify', ['name' => $name, 'verification_code' => $verification_code],
            function ($mail) use ($email, $name, $subject) {
                $mail->from(getenv('FROM_EMAIL_ADDRESS'), "From User/Company Name Goes Here");
                $mail->to($email, $name);
                $mail->subject($subject);
            });
        return response()->json(['success' => true, 'message' => 'Thanks for signing up! Please check your email to complete your registration.']);
    }
```

- Clearly, we will see json is returned when click button create. By the way, I want to use ajax to see the result, but if we didn't use Ajax in client, Let's use postman to see the result. It's better for now.
- Try to use dd(#request) and check how many paramaters is sent from client?

```php
Route::post('register', 'AuthController@register')->name('api.register');
```

```php
<div class="panel-heading">Register</div>
    <div class="panel-body">
        <form class="form-horizontal" method="POST" action="{{ route('api.register') }}">
            {{ csrf_field() }}
```

![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/5.png)
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/6.png)
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/7.png)
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/8.png)

<h1 class="title style-scope ytd-video-primary-info-renderer"><yt-formatted-string class="style-scope ytd-video-primary-info-renderer">Laravel 5.4 Tutorial | Send Email To Gmail #1  | Part 24 | Bitfumes</yt-formatted-string></h1>

- Where is my issue?

```php
Mail::send('email.verify', ['name' => $name, 'verification_code' => $verification_code],
            function ($mail) use ($email, $name, $subject) {
                $mail->from(getenv('FROM_EMAIL_ADDRESS'), "From User/Company Name Goes Here");
                $mail->to($email, $name);
                $mail->subject($subject);
            });
```

- Fix it right now

```php
Mail::send('email.verify', ['name' => $name, 'verification_code' => $verification_code],
    function ($mail) use ($email, $name, $subject) {
        $mail->from(getenv('gamebfme2test@gmail.com'), 'gamebfme2test@gmail.com');
        $mail->to($email, $name);
        $mail->subject($subject);
    });         
```

```php
MAIL_DRIVER=smtp
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=gamebfme2test@gmail.com
MAIL_PASSWORD=Abc@123456
MAIL_ENCRYPTION=tls
```
- Turn on the "Allow less secure apps" on? go to this link https://myaccount.google.com/security#connectedapps. It is owned of gamebfme2test@gmail.com.

- Go to https://accounts.google.com/UnlockCaptcha , and click continue and unlock your account for access through other media/sites.

![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/9.png)
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/10.png)

- We have token is generated and saved in table "verifycation_user". Here is example
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/11.png)

- We use it **eK3qvXHST1FVJ9SugIhnrdksNYbKWD** to verfiy and call this function here:

```php
/**
     * API Verify User
     *
     * @param Request $request
     * @return \Illuminate\Http\JsonResponse
     */
    public function verifyUser($verification_code)
    {
        $check = DB::table('user_verifications')->where('token', $verification_code)->first();
        if (!is_null($check)) {
            $user = User::find($check->user_id);
            if ($user->is_verified == 1) {
                return response()->json([
                    'success' => true,
                    'message' => 'Account already verified..'
                ]);
            }
            $user->update(['is_verified' => 1]);
            DB::table('user_verifications')->where('token', $verification_code)->delete();
            return response()->json([
                'success' => true,
                'message' => 'You have successfully verified your email address.'
            ]);
        }
        return response()->json(['success' => false, 'error' => "Verification code is invalid."]);
    }
```

- We check which route is call when user click link: **http://127.0.0.1:8000/user/verify/eK3qvXHST1FVJ9SugIhnrdksNYbKWD**
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/12.png)
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/13.png)

<p name="3a04" id="3a04" class="graf graf--p graf-after--p"><strong class="markup--strong markup--p-strong">Login</strong><br>Create a POST request to api/login with form-data under Body tab.</p>

### There are two ways:
#### Login with normal website: 
- Specify route to call:
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/14.png)
- Check database: 
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/15.png)
- Open url: http://127.0.0.1:8000/login
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/16.png)
- Result after login website successfully
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/17.png)

#### Login with API: 
- Specify api: ![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/17.png)
- Using postman to excute url: http://127.0.0.1:8000/api/login
- Change form
```php
<form class="form-horizontal" method="POST" action="{{ route('api.login') }}">
```
- Reesult: ![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/18.png)
- Reesult: ![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/19.png)

<p name="7c4c" id="7c4c" class="graf graf--p graf-after--figure"><strong class="markup--strong markup--p-strong">Try accessing test route with the token [GET]</strong></p>
- Link: http://127.0.0.1:8000/api/test?token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOi8vMTI3LjAuMC4xOjgwMDAvYXBpL2xvZ2luIiwiaWF0IjoxNTM2NjQ3MjEwLCJleHAiOjE1MzY2NTA4MTAsIm5iZiI6MTUzNjY0NzIxMCwianRpIjoiY05GMWJ5OU85QUJDcUlRTCIsInN1YiI6MTgsInBydiI6Ijg3ZTBhZjFlZjlmZDE1ODEyZmRlYzk3MTUzYTE0ZTBiMDQ3NTQ2YWEifQ.vovTS4lq77TDDbHlf9DBBSsjtN6N7Be9mcazGfyUmjc
- Result:
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/20.png)
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/21.png)

#### Research structure of 
- Research about **BaseMiddleware.php**
- Configure url with postman (add header to method, we need to call)
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/22.png)

#### Get value from header
```php
Route::group(['middleware' => ['jwt.auth']], function() {

    Route::get('logout', 'AuthController@logout');

    /*Route::get('test', function(){
        return response()->json(['foo'=>'bar']);
    });*/

    Route::get('test', 'TestController@index')->name('test');
});
```

```php
<?php
namespace App\Http\Controllers;

class TestController extends Controller
{
    public function index() {
        return response()->json(['foo'=>'Hello World']);
    }
}
```
- How to check request and get value from header
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/23.png)

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Support\Facades\Log;

class TestController extends Controller
{
    public function index() {
        Log::info(request()->header('api_token'));
        return response()->json(['foo'=>'Hello World']);
    }
}
```

- Result:

```
[2018-09-11 07:47:56] local.INFO:   
[2018-09-11 07:48:25] local.INFO: sfdsfdsfdsfs  

```

<h1 class="title style-scope ytd-video-primary-info-renderer"><yt-formatted-string class="style-scope ytd-video-primary-info-renderer">Advanced Debugging in PhpStorm - PhpStorm Video Tutorial</yt-formatted-string></h1>




#### How to authenticate with it.
- Open **BaseMiddleware.php**

```php
    public function checkForToken(Request $request)
    {
        \Illuminate\Support\Facades\Log::info(request()->header('api_token'));
        \Illuminate\Support\Facades\Log::info($request->header('api_token'));
        if (! $this->auth->parser()->setRequest($request)->hasToken()) {
            throw new UnauthorizedHttpException('jwt-auth', 'Token not provided');
            \Illuminate\Support\Facades\Log::info("Token is not provide!");
        }
    }
```

![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/24.png)
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/25.png)

#### Done for now
```php
    protected function setAuthenticationHeader($response, $token = null)
    {
        $token = $token ?: $this->auth->refresh();
        $response->headers->set('Authorization', 'Bearer '.$token);

        return $response;
    }
```
![alt text](https://github.com/phplorence/Tokend-Based-Laravel/blob/master/jwt/img/26.png)


## ISSUE PHP

# NOT LOAD VIEW CORRECTLY => DUMMY DATA ON IT VIEW
