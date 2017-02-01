# Upgrade Guide

- [Upgrading To Spark 4.0](#upgrade-spark-4.0)
- [Upgrading To Spark 3.0](#upgrade-spark-3.0)
- [Upgrading To Spark 2.0.11](#upgrade-spark-2.0.11)
- [Upgrading To Spark 2.0](#upgrade-spark-2.0)

<a name="upgrade-spark-4.0"></a>
## Upgrading To Spark 4.0

Spark 4.0 is a free upgrade for your Spark applications and provides compatibility with Laravel 5.4. In addition, new Spark 4.0 applications are configured to use [Laravel Mix](https://laravel.com/docs/mix) and Webpack instead of Laravel Elixir.

#### Via Spark CLI

If you installed Spark via the `spark` CLI tool, you may run the `spark:update` Artisan command:

    php artisan spark:update --major

#### Via Composer

If you installed Spark via Composer, you may simply upgrade your dependency in your `composer.json` file and run the `composer update` command:

    "laravel/spark": "~4.0"

### Updating NPM Packages / Elixir

In order to upgrade to Laravel Mix, you need to update the following packages in your `package.json` file:

- Replace `laravel-elixir` dependency with `laravel-mix: ^0.6.0`.
- Replace `vue-resource` dependency with `axios: ^0.15.2`.

You may also remove any other Elixir related dependencies from your `package.json` file. Next, add the following NPM scripts to your `package.json` file:

    "scripts": {
        "dev": "node node_modules/cross-env/bin/cross-env.js NODE_ENV=development node_modules/webpack/bin/webpack.js --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
        "watch": "node node_modules/cross-env/bin/cross-env.js NODE_ENV=development node_modules/webpack/bin/webpack.js --watch --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
        "hot": "node node_modules/cross-env/bin/cross-env.js NODE_ENV=development node_modules/webpack-dev-server/bin/webpack-dev-server.js --inline --hot --config=node_modules/laravel-mix/setup/webpack.config.js",
        "production": "node node_modules/cross-env/bin/cross-env.js NODE_ENV=production node_modules/webpack/bin/webpack.js --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js"
    },

Next, Remove your `gulpfile.js` and replace it with the `webpack.mix.js` file available in the [Spark repository](https://github.com/laravel/spark/blob/4.0/install-stubs/webpack.mix.js).

Once you have created your `webpack.mix.js` file, update the first line of your `resources/assets/less/app.less` file to reference the full path to the Bootstrap Less file, as can be [seen in the Spark repository](https://github.com/laravel/spark/blob/4.0/install-stubs/resources/assets/less/app.less#L1).

Once you have made these changes, you can run `npm install` and `npm run dev` to compile your assets using Mix.

<a name="upgrade-spark-3.0"></a>
## Upgrading To Spark 3.0

Spark 3.0 is a free upgrade for your Spark applications and provides compatibility with Vue 2.0. Before upgrading to Spark 3.0 you should review Vue's [migration guide](https://vuejs.org/guide/migration.html) and make sure your custom Vue Components are compatible with Vue 2.0.

#### Via Spark CLI

If you installed Spark via the `spark` CLI tool, you may run the `spark:update` Artisan command:

    php artisan spark:update

#### Via Composer

If you installed Spark via Composer, you may simply upgrade your dependency in your `composer.json` file and run the `composer update` command:

    "laravel/spark": "~3.0"

### Updating NPM Packages / Elixir

After upgrading to Spark 3.0, you need to update the following packages in your `package.json` file:

	"laravel-elixir": "^6.0.0-11",
	"laravel-elixir-vue-2": "^0.2.0",
	"vue": "~2.0.1",

You should also update your `gulpfile.js` to require `laravel-elixir-vue-2`:

	require('laravel-elixir-vue-2');

If you are using Webpack to compile your assets in your `gulpfile.js`, you should add the `laravel-elixir-webpack-official` package to your `package.json` file:

    npm install --save laravel-elixir-webpack-official

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
