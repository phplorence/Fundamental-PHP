# Fundamental-PHP
>> - [Blade Templating Engine](#blade-templating-engine)
# Blade Templating Engine

    Shiftx2 web.php
    Shiftx2 welcome.blade
    
    resources/views/layouts/master.blade.php
    copy[CONTENT] welcome.blade.php and paste master.blade.php
    
    type @yield("content") in <body></body> in master.blade.php
    type @extends('layouts.master') in welcome.blade.php
    
    ERROR 1: Content not in right section
   
    <h1>Nguyen Van Vuong</h1>
    <!doctype html>
    <html lang="en">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">

        <title>Laravel</title>

        <!-- Fonts -->
        <link href="https://fonts.googleapis.com/css?family=Raleway:100,600" rel="stylesheet" type="text/css">
    </head>
    <body>
        </body>
    </html>
    
    type @section('content') 
    <h1>Nguyen Van Vuong</h1>
    type @endsection
    
    create folder/partials 
    create header.blade.php inside folder(partials)
    type @include('partials.header') inside master.blade.php
    
    access the Internet and embed bootstrap online 
    
    type these codes below in **header.blade.php**
      <nav class="navbar navbar-default">
          <div class="container-fluid">
              <div class="navbar-header">
                  <a href="#" class="navbar-brand">Laravel Guide</a>
                  <ul class="nav navbar-nav">
                      <li><a href="#">Blog</a></li>
                      <li><a href="#">About</a></li>
                  </ul>
              </div>
          </div>
      </nav>
      
    type continue:
    
    @section('content')
        <div class="row">
            <div class="col-lg-12">
                <h1>Nguyen Van Vuong</h1>
            </div>
        </div>
    @endsection
    
    type content:
    
    @section('content')
        <div class="container">
            <div class="row">
                <div class="col-lg-12">
                    <h1>Control Structure</h1>
                    @if(true)
                        <p>This is only displays if it is true</p>
                    @else
                        <p>This is only displays if it is false</p>
                    @endif
                    @for($i = 0; $i < 5; $i++)
                        <p>{{ $i }}. Interaction</p>
                    @endfor
                </div>
            </div>
        </div>
    @endsection
    
    type command lines:
    
    <hr>
    <hr>
    <h2>XSS</h2>
    {{ "<script>alert('Hello')</script>" }}
    {!! "<script>alert('Hello')</script>" !!}
    <hr>
    
    type again content in welcome.blade.php
    
    @section('content')
        <div class="row">
            <div class="col-md-12">
                <p class="quote">The beautiful Laravel</p>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <h1 class="post-title">Learning Laravel</h1>
                <p>This blog post will get you right on track with Laravel!</p>
                <p><a href="#">Read more....</a></p>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <h1 class="post-title">The next Steps</h1>
                <p>Understanding the Basics is great, but you need to be able to make the next steps.</p>
                <p><a href="#">Read more...</a></p>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <h1 class="post-title">Laravel 5.3</h1>
                <p>Though announced as a "minor release", Laravel 5.3 ships with somer very interesting additions and features.</p>
                <p><a href="#">Read more...</a></p>
            </div>
        </div>
    @endsection
    
    Go to **public/css/styles.css**
    type CSS
    
    .quote {
        font: 400 72px "Arizonia", Helvetica, sans-serif;
        color: #F4645F;
        text-align: center;
    }

    .post-title {
        color: #F4645F;
    }

    Configure again URL to point CSS resource
    href = "{{ URL::to('css/styles.css') }}"
    
    Navigate **routes/web.php**
    type code:
    Route::get('post/{id}', function () {
       return view();
    });
    
    create folder such as: admin, blog, other
    admin cover create.blade.php, edit.blade.php and index.blade.php
    blog cover index.blade.php, post.blade.php
    layouts cover admin.blade.php, master.blade.php
    other cover about.blade.php
    partials cover admin-header.blade.php
    
    type again with **web.php**    
    Route::get('/', function () {
    return view('blog.index');
    });

    Route::get('post/{id}', function () {
       return view('blog.post');
    });

    Route::get('about', function (){
        return view('other.about');
    });

    Route::get('admin', function (){
       return view('admin.create');
    });

    Route::get('admin/edit/{id}', function () {
       return view('admin.edit');
    });
    
    type **admin/create.blade.php**
    @extends('layouts.admin')
    @section('content')
        <div class="row">
            <div class="col-md-12">
                <form action="{{ route('admin.create') }}" method="post">
                    <div class="form-group">
                        <label for="title">Title</label>
                        <input type="text" class="form-control" id="title" name="title">
                    </div>
                    <div class="form-group">
                        <label for="content">Content</label>
                        <input type="text" class="form-control" id="content" name="content">
                    </div>
                    <button type="submit" class="btn btn-primary">Submit</button>
                </form>
            </div>
        </div>
    @endsection
    
    type **admin/edit.blade.php**
    <?php
    /**
     * Created by PhpStorm.
     * User: vuongluis
     * Date: 4/7/2018
     * Time: 11:11 AM
     */
    ?>
    @extends('layouts.admin')
    @section('content')
        <div class="row">
            <div class="col-md-12">
                <form action="{{ route('admin.update') }}" method="post">
                    <div class="form-group">
                        <label for="title">Title</label>
                        <input type="text" class="form-control" id="title" name="title">
                    </div>
                    <div class="form-group">
                        <label for="content">Content</label>
                        <input type="text" class="form-control" id="content" name="content">
                    </div>
                    <button type="submit" class="btn btn-primary">Submit</button>
                </form>
            </div>
        </div>
    @endsection

    type **admin/index.blade.php**
    @extends('layouts.admin')

    @section('content')
        <div class="row">
            <div class="col-md-12">
                <a href="{{ route('admin.create') }}" class="btn btn-success">New Post</a>
            </div>
        </div>
        <hr>
        <div class="row">
            <div class="col-md-12">
                <p><strong>Learning Laravel</strong> <a href="{{ route('admin.edit', ['id' => 1]) }}">Edit</a></p>
            </div>
        </div>
    @endsection
    
    type **blog/index.blade.php**
    @extends('layouts.master')

    @section('content')
        <div class="row">
            <div class="col-md-12">
                <p class="quote">The beautiful Laravel</p>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12 text-center">
                <h1 class="post-title">Learning Laravel</h1>
                <p>This blog post will get you right on track with Laravel!</p>
                <p><a href="{{ route('blog.post', ['id' => 1]) }}">Read more...</a></p>
            </div>
        </div>
        <hr>
        <div class="row">
            <div class="col-md-12 text-center">
                <h1 class="post-title">The next Steps</h1>
                <p>Understanding the Basics is great, but you need to be able to make the next steps.</p>
                <p><a href="{{ route('blog.post', ['id' => 2]) }}">Read more...</a></p>
            </div>
        </div>
        <hr>
        <div class="row">
            <div class="col-md-12 text-center">
                <h1 class="post-title">Laravel 5.3</h1>
                <p>Though announced as a "minor release", Laravel 5.3 ships with somer very interesting additions and features.</p>
                <p><a href="{{ route('blog.post', ['id' => 3]) }}">Read more...</a></p>
            </div>
        </div>
    @endsection
    
    type **blog/post.blade.php**
    @extends('layouts.master')

    @section('content')
        <div class="row">
            <div class="col-md-12">
                <p class="quote">Learning Laravel</p>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Assumenda commodi illum nobis nostrum numquam officiis possimus provident rem repellat sint? Dicta eligendi eum hic, labore nisi non quidem quos voluptates.
                Lorem ipsum dolor sit amet, consectetur adipisicing elit. Culpa debitis magni modi nihil numquam odit pariatur recusandae repellendus sint voluptates. Blanditiis expedita minus nostrum numquam placeat porro praesentium quae soluta!</p>
            </div>
        </div>
    @endsection
    
    type **layouts/admin.blade.php**
    <?php
    /**
     * Created by PhpStorm.
     * User: vuongluis
     * Date: 4/7/2018
     * Time: 11:14 AM
     */
    ?>
    <!doctype html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport"
              content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>That are Laravel Views with Laravel Blade</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
        <link href='http://fonts.googleapis.com/css?family=Arizonia' rel='stylesheet' type='text/css'>
    </head>
    <body>
    @include('partials.admin-header')
    <div class="container">
        @yield('content')
    </div>
    </body>
    </html>
    
    type **other/about.blade.php**
    @extends('layouts.master')

    @section('content')
        <div class="row">
            <div class="col-md-12">
                <p class="quote">About Me</p>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Assumenda commodi illum nobis nostrum numquam officiis possimus provident rem repellat sint? Dicta eligendi eum hic, labore nisi non quidem quos voluptates.
                    Lorem ipsum dolor sit amet, consectetur adipisicing elit. Culpa debitis magni modi nihil numquam odit pariatur recusandae repellendus sint voluptates. Blanditiis expedita minus nostrum numquam placeat porro praesentium quae soluta!</p>
            </div>
        </div>
    @endsection
    
    type **partials/admin-header.blade.php**
    <?php
    /**
     * Created by PhpStorm.
     * User: vuongluis
     * Date: 4/7/2018
     * Time: 11:15 AM
     */
    ?>
    <nav class="navbar navbar-default">
        <div class="container-fluid">
            <div class="navbar-header">
                <a class="navbar-brand" href="{{ route('blog.index') }}">Laravel Guide</a>
                <ul class="nav navbar-nav">
                    <li class="active"><a href="{{ route('admin.index') }}">Posts</a></li>
                </ul>
            </div>
        </div><!-- /.container-fluid -->
    </nav>
    
    type code: <li><a href="/about">About</a></li> in **header.blade.php**
    
    adjust routes:
    Route::get('/', function () {
    return view('/blog.index');
    })->name('/blog.index');

    Route::get('post/{id}', function () {
        return view('blog.post');
    })->name('/blog.post');

    Route::get('about', function (){
        return view('other.about');
    })->name('other.about');

    Route::get('admin', function (){
        return view('admin.create');
    })->name('admin.create');

    Route::get('admin/edit/{id}', function () {
        return view('admin.edit');
    })->name('admin.edit');

    type code: <li><a href="{{ route('other.about') }}">About</a></li> in **header.blade.php**
    
    Route::get('about-page', function (){
      return view('other.about');
    })->name('other.about');
    
    Route::get('about', function (){
      return view('other.about');
    })->name('other.about');
    
    type <p><a href="{{ route('blog.post', ['id' => 1]) }}">Read more....</a></p> in **blog/index.blade.php**
    
    https://app.pluralsight.com/player?course=laravel-php-framework-getting-started-the-basics&author=max-schwarzmueller&name=laravel-php-framework-getting-started-the-basics-m3&clip=8&mode=live
    
    type code
    Route::post('admin/create', function () {
        return "It works now!";
    })->name('admin.create');

    Route::get('admin/edit/{id}', function () {
        return view('admin.edit');
    })->name('admin.edit');

    Route::post('admin/edit', function () {
        return "It works now!";
    })->name('admin.update');

    type <form action="{{ route('admin.create') }}" method="post"> in **admin/create.blade.php**
    type <form action="{{ route('admin.update') }}" method="post"> in **admin/edit.blade.php**
    
    
    
    
    
    
    
    
    
    

    
    
