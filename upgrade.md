# Upgrade Guide

- [Upgrading To Spark 6.0](#upgrade-spark-6.0)

<a name="upgrade-spark-6.0"></a>
## Upgrading To Spark 6.0

Spark 6.0 is the first significant update of Spark since its original release. As such, it includes an all-new user interface based on Bootstrap 4, may be fully localized, supports per-seat pricing, supports Stripe 3.0 using Stripe Elements, and is based on the latest version of Laravel.

While the back-end changes to from Spark 5.0 to Spark 6.0 are relatively minor, the front-end changes are **extensive**. Unfortunately, this is primarily due to the fact that Bootstrap 4 is radically different than Bootstrap 3 and is not backwards compatible in any way.

For this reason, you may wish to reserve usage of Spark 6.0 to new Spark projects only. Otherwise, your entire application front-end will need to be converted to Bootstrap 4. Spark 5 will continue to be maintained for at least 12 months following the release of Spark 6.

### License Upgrade Requirements

Spark 6.0 is a paid update. As such, you must purchase an upgraded license in order to access the source code. However, if you are an existing Spark customer, you will automatically receive a 50% discount when upgrading your Spark licenses to 6.0.

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

If you override the `SendInvitation` in your app, the `handle()` method now accepts a third argument `$role` which will be used to assign an initial role for the invited user.

### Changes in how you refer to teams

In Spark 5.0 you used the `Spark::referToTeamAs()` to change how teams are referred to in your application's views and URIs, in Spark 6.0 this method was removed and replaced with the following:

```php
`Spark::prefixTeamsAs('bands')`
```

This will cause all URIs that has the word `teams` to become `bands` instead.

To change how a team is referred to in your views you can update the `resources/lang/en/teams.php` translation file and swap all instances of the word "team" with the word of your choice.
