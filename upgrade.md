# Upgrade Guide

- [Upgrading To Spark 2.0.11 ](#upgrade-spark-2.0.11 )
- [Upgrading To Spark 2.0](#upgrade-spark-2.0)

<a name="upgrade-spark-2.0.11"></a>
## Upgrading To Spark 2.0.11

Spark 2.0.11 ships with a new option for GitHub-style teams that are path based. This feature is optional and if you are free to totally ignore it without any consequences in your Spark applications.

Before enabling this option on an existing Spark application you will need to add a "slug" field to your teams database table. An example migration for performing this action is included below; however, you are free to write your own migration and customize the slug creation however you like. Team slugs must be unique per team across the entire application:

```php
<?php

use Illuminate\Support\Str;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class AddSlugToTeams extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('teams', function (Blueprint $table) {
            $table->string('slug')->after('name')->nullable();
        });

        $this->generateSlugForExistingTeams();

        $this->makeSureSlugsAreUnique();
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('teams', function (Blueprint $table) {
            $table->dropColumn('slug');
        });
    }

    /**
     *  Generates a slug for existing teams.
     *
     *  $return void
     */
    private function generateSlugForExistingTeams()
    {
        App\Team::each(function($team){
            $team->update([
                'slug' => Str::slug($team->name)
            ]);
        });
    }

    /**
     * Ensure that all teams have a unique slug.
     *
     * @return void
     */
    private function makeSureSlugsAreUnique()
    {
        $duplicates = DB::table('teams')->select('slug')
                        ->groupBy('slug')
                        ->havingRaw('COUNT(*) > 1')
                        ->pluck('slug');

        App\Team::whereIn('slug', $duplicates->toArray())->each(function($team, $key){
            $team->update([
                'slug' => $team->slug.$key
            ]);
        });

        Schema::table('teams', function (Blueprint $table) {
            $table->unique('slug');
        });
    }
}
```

This migration will add a slug field to the teams table and generate a unique slug for each of the existing teams.

<a name="upgrade-spark-2.0"></a>
## Upgrading To Spark 2.0

Spark 2.0 is a free upgrade for your Spark applications and provides compatibility with Laravel 5.3.

Before upgrading to Spark 2.0 you should review the entire [Laravel 5.3 upgrade guide](https://laravel.com/docs/5.3/upgrade) and make any applicable changes to your application. There are no further code upgrades you need to make before using Spark 2.0 other than those documented in the Laravel upgrade guide. Once you have completed applying the changes listed in the Laravel 5.3 upgrade guide, you are ready to upgrade your underlying Spark dependency.

#### Via Spark CLI

If you installed Spark via the `spark` CLI tool, you may run the `spark:update` Artisan command:

    php artisan spark:update

#### Via Composer

If you installed Spark via Composer, you may simply upgrade your dependency in your `composer.json` file and run the `composer update` command:

    "laravel/spark": "~2.0"

> You should also make sure to update your Cashier dependencies to `~7.0` for Stripe or `~2.0` if you are using Braintree.
