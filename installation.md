# Installation

- [Requirements](#requirements)
- [Installation](#installation)
    - [Installation Via Composer](#installation-via-composer)
    - [Linking The Storage Directory](#linking-the-storage-directory)
- [Installation Types](#installation-types)
- [Updating Spark](#updating-spark)

<a name="requirements"></a>
## Requirements

Laravel Spark has a few requirements you should be aware of before installing:

- Composer
- Laravel Framework 5.3+
- Laravel Elixir
- Bootstrap 3 (LESS)

The [Vue.js](http://vuejs.org) JavaScript framework is utilized on the registration and settings pages; however, you are free to build the "bulk" of your application using the JavaScript framework of your choice, or no JavaScript framework at all.

<a name="installation"></a>
## Installation

<a name="installation-via-composer"></a>
### Installation Via Composer

Spark provides a [Satis repository](https://spark-satis.laravel.com) which makes it simple to install Spark just like any other Composer package. First, make sure you have purchased a Spark license and [joined the Spark GitHub repository](/settings#/github).

Create a new Laravel project using the [Laravel installer](https://laravel.com/docs/installation):

    laravel new project-name

Next, add the following repository to your `composer.json` file:

    "repositories": [
        {
            "type": "composer",
            "url": "https://spark-satis.laravel.com"
        }
    ],

You should also add the following dependencies to your `composer.json` file's `require` section:

    "laravel/spark": "~2.0",
    "laravel/cashier": "~7.0"

Next, run the `composer update` command. You may be prompted for a GitHub token in order to install the private Spark repository. Composer will provide a link where you can create this token. Once the dependencies are installed, add the following service providers to your `app.php` configuration file:

    Laravel\Spark\Providers\SparkServiceProvider::class,
    Laravel\Cashier\CashierServiceProvider::class,

Next, run the `spark:install --force` command:

    php artisan spark:install --force

Once Spark is installed, add the following provider to your `app.php` configuration file:

    App\Providers\SparkServiceProvider::class,

Finally, you are ready to run the `npm install`, `gulp`, and `php artisan migrate` commands. Once these commands have completed, your ready to enjoy Spark!

<a name="linking-the-storage-directory"></a>
### Linking The Storage Directory

Once Spark is installed, you should link the `public/storage` directory to your `storage/app/public` directory. Otherwise, user profile photos stored on the local disk will not be available:

    php artisan storage:link

<a name="installation-types"></a>
## Installation Types

The default `spark:install` command will generate a project that supports individual user plans and Stripe for credit card processing. However, you may also create projects that use team billing or Braintree as your payment processor.

    php artisan spark:install project-name --braintree

    php artisan spark:install project-name --team-billing

    php artisan spark:install project-name --team-billing --braintree
