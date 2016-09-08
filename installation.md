# Installation

- [Requirements](#requirements)
- [Installation](#installation)
    - [Spark Installer](#spark-installer)
    - [Installation On Windows](#installation-on-windows)
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

<a name="spark-installer"></a>
### Spark Installer

#### Downloading The Installer

Laravel Spark offers a convenient installer that will create a new Laravel application and install Spark in the directory of your choice. Before getting started, make sure you have the `laravel/installer` package globally installed using Composer. You should make sure your version of the installer is >= `1.3.4`:

    composer global require laravel/installer

> **Note:** The Spark installer is only recommended for those on Mac or Linux systems. If you are using Windows, please install via Composer. Before using the Spark installer, make sure you have installed the [Laravel installer](https://laravel.com/docs/installation#installing-laravel).

First, clone the `laravel/spark-installer` repository from GitHub into any location on your machine. Once you have cloned the installer, be sure to run the `composer install` command within the cloned directory so the installer's dependencies will be installed.

Next, make sure the `spark-installer` directory is added to your systems `$PATH` variable, so that your machine will be able to locate the `spark` executable when you issue Spark commands.

#### Registering Your API Token

Now that you have the Spark installer, you need to register your Spark API token with the installer. You may create an API token from the [API settings dashboard](/settings#/api). Once you have created a token, register it with the installer using the `register` command:

    spark register token-value

If you would like to view the currently registered token, you may use the `token` command:

    spark token

#### Creating Spark Projects

Once you have installed the Spark installer and registered your API token, you may create new Spark projects using the `new` command:

    spark new project-name

This command will create a new Laravel project in a directory matching the given `project-name`. Spark will then be installed into the project and configured automatically.

> **Note:** Once the new Spark project has been created, you will need to migrate your new project's database using the `php artisan migrate` Artisan command.

Once Spark is installed, you are ready to [configure your application](/docs/1.0/billing).

<a name="manual-download"></a>
### Via Manual Download

You may also download a Spark "zip" archive from the [releases](/releases) page of this website. Download this zip archive and extract it into a `spark` folder within your project root. Next, add the `spark` directory as a repository to the project's `composer.json` file:

    "repositories": [
        {
            "type": "path",
            "url": "./spark"
        }
    ]

Next, you need to add the `laravel/spark` requirement to your `composer.json` file:

    "require": {
        "php": ">=5.5.9",
        "laravel/framework": "5.2.*",
        "laravel/cashier": "~6.0",
        "laravel/spark": "*@dev"
    }

Next, run the `composer update` command in your project root. Once the dependencies have been installed, add the `Laravel\Spark\Providers\SparkServiceProvider` class to your application's `providers` option in your `app.php` configuration file.

Finally, you are ready to run the `spark:install` command:

    php artisan spark:install --force

Once this command has run, you need to add two more additional service providers to your `app.php` configuration file:

    Laravel\Spark\Providers\SparkServiceProvider::class,
    App\Providers\SparkServiceProvider::class,
    Laravel\Cashier\CashierServiceProvider::class,

> **Note:** Once Spark has been installed, don't forget to run `npm install`, `gulp`, and `php artisan migrate`.

<a name="installation-on-windows"></a>
### Installation On Windows

If you are installing Spark on a Windows machine, you should use install Spark [via it's Satis Composer repository](#installation-via-composer).

<a name="installation-via-composer"></a>
### Installation Via Composer

Spark provides a [Satis repository](https://spark-satis.laravel.com) which makes it simple to install Spark just like any other Composer package. First, make sure you have purchased a Spark license and [joined the Spark GitHub repository](/settings#/github).

Create a new Laravel project using the [Laravel installer](https://laravel.com/docs/installation):

    laravel new project-name --5.2

Next, add the following repository to your `composer.json` file:

    "repositories": [
        {
            "type": "composer",
            "url": "https://spark-satis.laravel.com"
        }
    ],

You should also add the following dependencies to your `composer.json` file's `require` section:

    "laravel/spark": "~1.0",
    "laravel/cashier": "~6.0"

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

    ln -s /path/to/storage/app/public /path/to/public/storage

<a name="installation-types"></a>
## Installation Types

The default `spark new` command will generate a project that supports individual user plans and Stripe for credit card processing. However, you may also create projects that use team billing or Braintree as your payment processor.

    spark new project-name --braintree

    spark new project-name --team-billing

    spark new project-name --team-billing --braintree

<a name="updating-spark"></a>
## Updating Spark

You may update your Spark application using the `spark:update` Artisan command:

    php artisan spark:update

This command will download and install the latest release of Spark into your application and update any views that have not already been customized. Views that have already been customized will not be updated.
