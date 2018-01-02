# Upgrade Guide

- [Upgrading To Spark 6.0](#upgrade-spark-6.0)

<a name="upgrade-spark-6.0"></a>
## Upgrading To Spark 6.0

Spark 6.0 comes with a fresh Bootstrap4 theme, it also uses Lodash instead of Underscore when dealing with JavaScript collections.

#### Via Spark CLI

If you installed Spark via the `spark` CLI tool, you may run the `spark:update` Artisan command:

    php artisan spark:update --major

#### Via Composer

If you installed Spark via Composer, you may simply upgrade your dependency in your `composer.json` file and run the `composer update` command:

    "laravel/spark": "~6.0"

Once you have made these changes, you can run `npm run dev` to compile your assets.

### Database Schema Changes

Spark 6.0 has a new feature for applications using team roles, this feature allows you to select the role that will be assigned to the invited user after he signs up.

For this to work, you need to run the following migration:

```php
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
```


### SendInvitation Interaction changes

If you override the `SendInvitation` in your app, the `handle()` method now accepts a third argument `$role` which will be used to assign an initial role for the invited user.


### Changes in how you refer to teams

In Spark 5.0 you used the `Spark::referToTeamAs()` to change how teams are referred to in your application's views and URIs, in Spark 6.0 this method was removed and replaced with the following:

```php
`Spark::prefixTeamsAs('bands')`
```

This will cause all URIs that has the word `teams` to become `bands` instead.

To change how a team is referred to in your views you can update the `resources/lang/en/teams.php` translation file and swap all instances of the word "team" with the word of your choice.