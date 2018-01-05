# Upgrade Guide

- [Upgrading To Spark 6.0](#upgrade-spark-6.0)

<a name="upgrade-spark-6.0"></a>
## Upgrading To Spark 6.0

Spark 6.0 is the first significant update of Spark since its original release. As such, it includes an all-new user interface based on Bootstrap 4, may be fully localized, supports per-seat pricing, supports Stripe 3.0 using Stripe Elements, and is based on the latest version of Laravel.

While the back-end changes to from Spark 5.0 to Spark 6.0 are relatively minor, the front-end changes are **extensive**. Unfortunately, this is primarily due to the fact that Bootstrap 4 is radically different than Bootstrap 3 and is not backwards compatible in any way.

For this reason, you may wish to reserve usage of Spark 6.0 to new Spark projects only. Otherwise, your entire application front-end will need to be converted to Bootstrap 4. Spark 5 will continue to be maintained for at least 12 months following the release of Spark 6.

### License Upgrade Requirements

Due to the extensive modifications and improvements, Spark 6.0 is the first paid update to Spark since it was originally released in 2016. As such, you must purchase an upgraded license in order to access the source code. **However, if you are an existing Spark customer, you will automatically receive a 50% discount when upgrading your Spark licenses to 6.0.**

After purchasing an upgraded license, you may join the Spark 6.0 repository from your account profile on the Spark website.

### Spark Series Names

Since multiple versions of Spark are included in a paid upgrade, Spark uses "series" names to refer to a series of releases that include a single paid upgrade. Spark 6.0 begins this convention by using the "Aurelius" series name and the `laravel/spark-aurelius` repository. This repository will continue to be used for future Spark versions until the next paid upgrade. Paid upgrades are always released at least one full year apart from each other.

### Upgrading Via Spark CLI

If you installed Spark via the `spark` CLI tool, you may run the `spark:update` Artisan command:

    php artisan spark:update --major

### Upgarding Via Composer

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

### Team Localization Changes

In prior releases of Spark, the `Spark::referToTeamAs()` method was used to change how teams are referred to in your application's views and URIs. However, in Spark 6.0, this method has been removed and replaced with the `prefixTeamsAs` method:

    Spark::prefixTeamsAs('bands')

This method will instruct Spark to use `bands` in all team URLs instead of `teams`. To change the word used to refer to "teams" in your application's views, you should update the `resources/lang/en/teams.php` translation file.

### Upgrading to Bootstrap4

Bootstrap4 is the first upgrade to the famous CSS framework in a long time, it comes with some changes like switching from Less to Sass, Flexbox-based grid system, dropping Glyphicons icon font, and adding many new utility classes.

For a complete guide on how to upgrade your application to Bootstrap4 you can check the [official migration guide](https://getbootstrap.com/docs/4.0/migration/). However, for your modified Spark views or custom components you can easily upgrade your markup and classes by applying the following changes:

- Switch `.panel`, `.panel-header`, and `.panel-body` to `.card`, `.card-header`, and `.card-body`.
- Remove the `.form-horizontal` class from your forms and add a `.row` class in each of your `.form-group` elements.
- Replace the `.control-label` class with `col-form-label`.
- Replace the `.help-block` class with `.invalid-feedback`.
- For validation errors, instead of adding the `.has-error` class to your `.form-group`, add a `.is-invalid` class to your `.form-control` input.
- Replace all grid offset classes (e.g. `col-md-offset-4`) to the new class names `.offset-md-4`.
- Replace `.btn-default` with `.btn-secondary`.