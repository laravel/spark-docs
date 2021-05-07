# Installation

- [Requirements](#requirements)
- [Installation](#installation)
    - [Installation Via Composer](#installation-via-composer)
    - [Linking The Storage Directory](#linking-the-storage-directory)
- [Installation Types](#installation-types)

<a name="requirements"></a>
## Requirements

Laravel Spark has a few requirements you should be aware of before installing:

- Composer
- Laravel Framework 6.0+
- Laravel Mix
- Laravel Cashier
- Laravel UI
- Bootstrap 4 (SASS)
- Node.js & NPM

Before installing Spark, make sure you have the latest version of Node and NPM. You can download a graphical installer for Node on the [Node website](https://nodejs.org).

The [Vue.js](http://vuejs.org) JavaScript framework is utilized on the registration and settings pages; however, you are free to build the "bulk" of your application using the JavaScript framework of your choice, or no JavaScript framework at all.

<a name="installation"></a>
## Installation

<a name="installation-via-composer"></a>
### Installation Via Composer

Spark provides a [Satis repository](https://spark-satis.laravel.com) which makes it simple to install Spark just like any other Composer package. First, make sure you have purchased a Spark license and [joined the Spark GitHub repository](/settings#/github).

Create a new Laravel project using the [Laravel installer](https://laravel.com/docs/installation):

    laravel new project-name

Next, add the following dependencies to your `composer.json` file's `require` section:

    "laravel/spark-aurelius": "~11.0",
    "laravel/ui": "^2.0",

Next, run the `composer update` command. You may be prompted for a GitHub token in order to install the private Spark repository. Composer will provide a link where you can create this token. Once the dependencies are installed, add the following service providers to your `app.php` configuration file:

    Laravel\Spark\Providers\SparkServiceProvider::class,
    Laravel\Cashier\CashierServiceProvider::class,

Next, run the `spark:install --force` command:

    php artisan spark:install --force

Once Spark is installed, add the following provider to your `app.php` configuration file:

    App\Providers\SparkServiceProvider::class,

Finally, you are ready to run the `npm install`, `npm run dev`, and `php artisan migrate` commands. Once these commands have completed, you are ready to enjoy Spark!

<a name="linking-the-storage-directory"></a>
### Linking The Storage Directory

Once Spark is installed, you should link the `public/storage` directory to your `storage/app/public` directory. Otherwise, user profile photos stored on the local disk will not be available:

    php artisan storage:link

<a name="installation-types"></a>
## Installation Types

The default `spark` command will generate a project that supports individual user plans. However, you may also create projects that use team billing.


    spark new project-name --team-billing

