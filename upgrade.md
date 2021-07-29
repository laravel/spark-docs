# Upgrade Guide

- [Upgrading To Spark 11.0](#upgrade-spark-11.0)
- [Upgrading To Spark 12.0](#upgrade-spark-12.0)

<a name="upgrade-spark-12.0"></a>
## Upgrading To Spark 12.0

Spark 12.0 provides compatibility with Cashier 13.

### Upgrading Via Spark CLI

If you installed Spark via the `spark` CLI tool, you may run the `spark:update` Artisan command:

    php artisan spark:update --major

### Upgrading Via Composer

If you installed Spark via Composer, you may simply update your dependency version in your `composer.json` file and run the `composer update` command. Of course, in order for your GitHub user to access the repository, you should first join this repository in the Spark dashboard:

    "laravel/spark-aurelius": "~12.0"

### Adding a tax_rates Table

Add the following migration in your project and then run `php artsian migrate`:

    class CreateTaxRatesTable extends Migration
    {
        public function up()
        {
            Schema::create('tax_rates', function (Blueprint $table) {
                $table->id();
                $table->string('stripe_id')->index();
                $table->double('percentage')->index();
                $table->timestamps();
            });
        }

        public function down()
        {
            Schema::dropIfExists('tax_rates');
        }
    }



### Cashier Upgrade Guide

After updating your Spark application, please review and make the necessary changes for Cashier 13.0 based on the [Cashier upgrade guide](https://github.com/laravel/cashier/blob/13.x/UPGRADE.md).

<a name="upgrade-spark-11.0"></a>
## Upgrading To Spark 11.0

Spark 11.0 provides compatibility with Laravel 8 and Cashier 12.

### Upgrading Via Spark CLI

If you installed Spark via the `spark` CLI tool, you may run the `spark:update` Artisan command:

    php artisan spark:update --major

### Upgrading Via Composer

If you installed Spark via Composer, you may simply update your dependency version in your `composer.json` file and run the `composer update` command. Of course, in order for your GitHub user to access the repository, you should first join this repository in the Spark dashboard:

    "laravel/spark-aurelius": "~11.0"

### Updating Your SparkServiceProvider

Rename the `booted()` method of the `SparkServiceProvider` to `boot()` and call `parent::boot()`.

    public function boot()
    {
        parent::boot();

        Spark::noCardUpFront()->trialDays(10);

        Spark::freePlan()
            ->features([
                'First', 'Second', 'Third'
            ]);

        Spark::plan('Basic', 'provider-id-1')
            ->price(10)
            ->features([
                'First', 'Second', 'Third'
            ]);
    }

Next, if you would like to keep your model classes in the `App` namespace, you may use the `useUserModel` and `useTeamModel` methods in the `register` method of your `SparkServiceProvider`:

    Spark::useUserModel('App\User');

    Spark::useTeamModel('App\Team');

### Cashier Upgrade Guide

After updating your Spark application, please review and make the necessary changes for Cashier 12.0 based on the [Cashier upgrade guide](https://github.com/laravel/cashier/blob/12.x/UPGRADE.md).


