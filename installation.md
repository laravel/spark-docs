# Installation

- [Requirements](#requirements)
- [Installation](#installation)
    - [Spark Installer](#spark-installer)
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

<a name="spark-installer"></a>
### Spark Installer

#### Downloading The Installer

Laravel Spark offers a convenient installer that will create a new Laravel application and install Spark in the directory of your choice. Before getting started, make sure you have the `laravel/installer` package globally installed using Composer. You should make sure your version of the installer is >= `2.3.0`:

    composer global require laravel/installer

> **Note:** The Spark installer is only recommended for those on Mac or Linux systems. If you are using Windows, please install via Composer. Before using the Spark installer, make sure you have installed the [Laravel installer](https://laravel.com/docs/installation#installing-laravel).

First, clone the `laravel/spark-installer` repository [from GitHub](https://github.com/laravel/spark-installer) into any location on your machine.

```
git clone https://github.com/laravel/spark-installer.git
```

Once you have cloned the installer, be sure to run the `composer install` command within the cloned directory so the installer's dependencies will be installed.

Next, make sure the `spark-installer` directory is added to your systems `$PATH` variable, so that your machine will be able to locate the `spark` executable when you issue Spark commands.

#### Registering Your API Token

Now that you have the Spark installer, you need to register your Spark API token with the installer. You may create an API token from the [API settings dashboard](/settings#/api). Once you have created a token, register it with the installer using the `register` command:

    spark register token-value

If you would like to view the currently registered token, you may use the `token` command:

    spark token

#### Creating Spark Projects

Once you have installed the Spark installer and registered your API token, you may create new Spark projects using the `new` command:

    spark new project-name

This command will create a new Laravel project in a directory matching the given `project-name`. Spark will then be automatically installed into the project and configured.

> **Note:** Once the new Spark project has been created, you will need to migrate your new project's database using the `php artisan migrate` Artisan command. If you are not using Homestead, you will also need to update your `.env` file with your database credentials.

Once Spark is installed, you are ready to [configure your application](/docs/10.0/billing).

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

    "laravel/spark-aurelius": "~10.0",
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

