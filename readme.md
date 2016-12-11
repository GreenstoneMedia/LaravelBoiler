## Asheville Laravel Meetup

Thank you to everyone that came out and participated in the first Laravel Meetup at Base Camp AVL!

This repository contains the project that was built during the meet up, but not including the example object creation.
Only the laracogs starter kit and the bootstrap preparation. This project will give you everything you need to start a
fresh Laravel 5.3 application with an admin dashboard, user authentication, and user verification. Please see the bottom 
of this file for resources about any of the components of the project. Feel free to send an email to daniel@greenstoneweb.com
with any questions about the project. Happy Coding!

The first week project encompassed installing the Laracogs Starter Kit with Bootstrap.
To get the final result up and running, just clone this project and then run 

    composer install 
    yarn 
    gulp
    php artisan migrate --seed

Requirements for this development environment include having the following resources available on your machine. 
See the resources section at the end of this file for links on installation steps for any of the packages.
    
    composer (with laravel package) 
    node (with gulp and (optional) yarn packages)
    Homestead or at least a local php engine and mysql database


## Setup Steps (Fresh Install and Setup)

Create a new laravel project with 

    laravel new truck_tracker
    cd truck_tracker
    composer install
    cp .env.example .env
    php artisan key:generate

Update the .env file and change 
- The database to whatever database you are using 
- The url property to the URL of your application
- Update the mail properties with your [mailtrap.io](http://mailtrap.io) credentials. If you do not provide an email solution, you should remove
the "active" middleware that comes with this setup in order to use the admin functionality without activating your account

## Install Laracogs

The full steps to install laracogs can be found [here (Laracogs Starter Docs)](https://laracogs.com/docs/kits/starter/)

The abbreviated installation steps are below.

`composer require yab/laracogs`

add `Yab\Laracogs\LaracogsProvider::class,` to config/app.php under Package Service Providers

Add the following to your `app/Http/Kernel.php` $routeMiddleware array.

     'admin' => \App\Http\Middleware\Admin::class,
     'permission' => \App\Http\Middleware\Permission::class,
     'roles' => \App\Http\Middleware\Roles::class,
     'active' => \App\Http\Middleware\Active::class,
     
Update the `App\User::class` in: 'config/auth.php' and 'database/factory/ModelFactory.php' to `App\Models\User::class`
    
Add the following to 'app/Providers/AuthServiceProvider.php' in the boot method
    
    Gate::define('admin', function ($user) {
        return ($user->roles->first()->name === 'admin');
    });
    
    Gate::define('team-member', function ($user, $team) {
        return ($user->teams->find($team->id));
    });
    
Add the following to 'app/Providers/EventServiceProvider.php' in the $listen property

    'App\Events\UserRegisteredEmail' => [
        'App\Listeners\UserRegisteredEmailListener',
    ],
    
You will also need to set the location of the email for password reminders. (config/auth.php - at the bottom)
    
    'passwords' => [
        'users' => [
            'provider' => 'users',
            'email' => 'emails.password',
            'table' => 'password_resets',
            'expire' => 60,
        ],
    ],
    
Install the package with `php artisan laracogs:starter`

Run `composer dump-autoload` 

Then ssh into your vagrant machine if you're using Homestead, or run the following from your 
terminal if you are using a local database

`php artisan migrate --seed`

You should now have a laravel application with authentication and login capability, but no styling

Run `php artisan laracogs:bootstrap` then `gulp` to update all of your forms and inner pages to use twitter bootstrap

You should now be able to visit (yourSite.org)/login and view a login page. You can log in with 

    Username : admin@admin.com
    Password : admin
    
You now have a laravel 5.3 application built with laracogs and ready for you to start using the Crudmaker to generate
your object models.

## Resources
- [Laravel](https://laravel.com/docs/5.3/)
- [Laracogs](https://laracogs.com/)
- [Homestead](https://laravel.com/docs/5.3/homestead)
- [Node](http://blog.teamtreehouse.com/install-node-js-npm-mac)
- [Bootstrap](http://getbootstrap.com/)
- [Mailtrap.io](http://mailtrap.io)