# Upgrade Guide

- [Upgrading To Spark 6.0](#upgrade-spark-6.0)

<a name="upgrade-spark-6.0"></a>
## Upgrading To Spark 6.0

Spark 6.0 is the first significant update of Spark since its original release. As such, it includes an all-new user interface based on Bootstrap 4, may be fully localized, supports per-seat pricing, supports Stripe 3.0 using Stripe Elements, and is based on the latest version of Laravel.

While the back-end changes to from Spark 5.0 to Spark 6.0 are relatively minor, the front-end changes are **extensive**. Unfortunately, this is primarily due to the fact that Bootstrap 4 is radically different than Bootstrap 3 and is not backwards compatible in any way.

For this reason, you may wish to reserve usage of Spark 6.0 to new Spark projects only. Otherwise, your entire application front-end will need to be converted to Bootstrap 4. **Spark 5 will continue to be maintained for at least 12 months following the release of Spark 6.**

### License Upgrade Requirements

Due to the extensive modifications and improvements, Spark 6.0 is the first paid update to Spark since it was originally released in 2016. As such, you must purchase an upgraded license in order to access the source code. **However, if you are an existing Spark customer, you will automatically receive a 50% discount when upgrading your Spark licenses to 6.0.**

After purchasing an upgraded license, you may join the Spark 6.0 repository from your account profile on the Spark website.

### Spark Series Names

Since multiple versions of Spark are included in a paid upgrade, Spark uses "series" names to refer to a series of releases that include a single paid upgrade. Spark 6.0 begins this convention by using the "Aurelius" series name and the `laravel/spark-aurelius` repository. This repository will continue to be used for future Spark versions until the next paid upgrade. Paid upgrades are always released at least one full year apart from each other.

### Upgrading The Spark Installer

Laravel Spark offers a convenient installer that will create a new Laravel application and install Spark in the directory of your choice. This installer needs to be upgraded in order to create fresh Spark 6.0 applications.

Before getting started, make sure you have the `laravel/installer` package globally installed using Composer. You should make sure your version of the installer is >= `1.3.4`:

    composer global require laravel/installer

> **Note:** The Spark installer is only recommended for those on Mac or Linux systems. If you are using Windows, please install via Composer. Before using the Spark installer, make sure you have installed the [Laravel installer](https://laravel.com/docs/installation#installing-laravel).

Next, clone the `laravel/spark-installer` repository [from GitHub](https://github.com/laravel/spark-installer) into any location on your machine. Once you have cloned the installer, be sure to run the `composer install` command within the cloned directory so the installer's dependencies will be installed.

> **Note:** If you have previously installed the Spark installer, you should run the `git pull origin master` command on the repository to retrieve the latest installer changes.

Next, make sure the `spark-installer` directory is added to your systems `$PATH` variable, so that your machine will be able to locate the `spark` executable when you issue Spark commands.

### Upgrading Via Spark CLI

If you installed Spark via the `spark` CLI tool, you may run the `spark:update` Artisan command:

    php artisan spark:update --major

### Upgrading Via Composer

If you installed Spark via Composer, you may simply upgrade your dependency name and version in your `composer.json` file and run the `composer update` command. Of course, in order for your GitHub user to access the repository, you should first join this repository in the Spark dashboard:

    "laravel/spark-aurelius": "~6.0"

Once you have made these changes, you can run `npm run dev` to compile your assets.

### Database Schema Changes

Spark 6.0 allows you to choose the role that will be assigned to a user when inviting them to a team. In order to support this feature, you should run the following database migration within your Spark application. You may create the `add_role_to_invitiations` migration manually and paste the following code into the file:

    <?php

    use Illuminate\Support\Facades\Schema;
    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Database\Migrations\Migration;

    class AddRoleToInvitations extends Migration
    {
        /**
         * Run the migrations.
         *
         * @return void
         */
        public function up()
        {
            Schema::table('invitations', function (Blueprint $table) {
    		     $table->string('role')->nullable();
            });
        }

        /**
         * Reverse the migrations.
         *
         * @return void
         */
        public function down()
        {
            Schema::table('teams', function (Blueprint $table) {
                $table->dropColumn('role');
            });
        }
    }

### SendInvitation Interaction Changes

The `handle` method of the `SendInvitation` interaction now accepts a third argument (`$role`) which is used to assign an initial role to the invited user.

### Publishing Language Files

Spark 6 is fully localizable. To get started, publish the language files to your application using the `vendor:publish` Artisan command. This command will create `/resources/lang/en/teams.php` and `/resources/lang/en.json` language files that you can customize according to your application's needs:

    php artisan vendor:publish --tag=spark-lang

### Team Localization Changes

In prior releases of Spark, the `Spark::referToTeamAs()` method was used to change how teams are referred to in your application's views and URIs. However, in Spark 6.0, this method has been removed and replaced with the `prefixTeamsAs` method:

    Spark::prefixTeamsAs('bands')

This method will instruct Spark to use `bands` in all team URLs instead of `teams`. To change the word used to refer to "teams" in your application's views, you should update the `resources/lang/en/teams.php` translation file.

### Updating Your `package.json` Dependencies

In your `package.json` file, update your `bootstrap` dependency to the latest version:

    "bootstrap": "^4.0",

Additionally, add the following dependencies:

    "popper.js": "^1.12",
    "lodash": "^4.17.4",

Finally, since Spark 6 uses [Lodash](https://lodash.com/), you may remove the `underscorejs` dependency.

### Updating Your Assets

Bootstrap 4 is written in SASS instead of Less. So, create a new `sass` directory within your `resources/assets` directory. Next, add an `app.scss` file in the new `sass` directory with the following content:

    @import "./../../../vendor/laravel/spark-aurelius/resources/assets/sass/spark";

    // Or, if you've published the Spark source to your project root...
    @import "./spark/spark";

Next, you should move any Less customizations from your old `app.less` file to the new `app.scss` file.

### Updating The Webpack Configuration

In your `webpack.mix.js` file, replace the `less` compilation line with the following `sass` command:

    mix.sass('resources/assets/sass/app.scss', 'public/css')

Next, you should delete the command below:

    .copy('node_modules/sweetalert/dist/sweetalert.css', 'public/css/sweetalert.css')

### Bootstrap 4

For a complete guide on how to upgrade your application's views to use Bootstrap 4 check out the [official migration guide](https://getbootstrap.com/docs/4.0/migration/).

If you have modified your vendor published Spark views or components, you can upgrade the views by applying the following preliminary changes:

- Change `.panel`, `.panel-heading`, and `.panel-body` to `.card`, `.card-header`, and `.card-body`.
- Replace the `.btn-default` class with the `.btn-secondary` class.
- Remove the `.form-horizontal` class from your forms.
- Add the `.row` class to each of your `.form-group` elements.
- Replace the `.control-label` class with the `col-form-label` class.
- Replace the `.help-block` class with `.invalid-feedback` class.
- For validation errors, remove the `.has-error` class from your `.form-group` elements and add the `.is-invalid` class to your `.form-control` elements.
- Replace all grid offset classes (`col-md-offset-4`, etc.) with their new class names (`.offset-md-4`, etc.).
