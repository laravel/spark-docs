# Upgrade Guide

- [Upgrading To Spark 9.0](#upgrade-spark-9.0)

<a name="upgrade-spark-9.0"></a>
## Upgrading To Spark 9.0

Spark 9.0 provides compatibility with Laravel 6.0 and Cashier 10.0. Cashier 10 includes support for European [Strong Customer Authentication (SCA)](https://stripe.com/docs/strong-customer-authentication) regulations.

**Spark 9.0 drops support for the Braintree payment system.**

### Upgrading Via Spark CLI

If you installed Spark via the `spark` CLI tool, you may run the `spark:update` Artisan command:

    php artisan spark:update --major

### Upgrading Via Composer

If you installed Spark via Composer, you may simply update your dependency version in your `composer.json` file and run the `composer update` command. Of course, in order for your GitHub user to access the repository, you should first join this repository in the Spark dashboard:

    "laravel/spark-aurelius": "~9.0"

### Database Schema Changes

Cashier 10.0 uses webhooks to update subscription statuses for users of your application. For this to work, you need to add a `stripe_status` to your subscription tables. You may create the `add_stripe_status_to_subscriptions` migration manually and paste the following code into the file:

```
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class AddStripeStatusToSubscriptions extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('subscriptions', function (Blueprint $table) {
			 $table->string('stripe_status')->nullable();
        });

        Schema::table('team_subscriptions', function (Blueprint $table) {
			 $table->string('stripe_status')->nullable();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('subscriptions', function (Blueprint $table) {
            $table->drop('stripe_status');
        });

        Schema::table('team_subscriptions', function (Blueprint $table) {
            $table->drop('stripe_status');
        });
    }
}
```

### Remove Unnecessary Event Listeners

Remove the following listeners from your `App\Providers\EventServiceProvider` file:

- `UpdateOwnerSubscriptionQuantity`
- `UpdateOwnerSubscriptionQuantity `
- `UpdateTeamSubscriptionQuantity `
- `TeamMemberRemoved`

### Updating Your Language Files

Add the following translation line to your language files:

```
"Please :linkOpen confirm your payment :linkClose to activate your subscription!": "Please :linkOpen confirm your payment :linkClose to activate your subscription!"
```

### The `Spark::useStripe()` Method

Since Spark 9.0 only supports Stripe, this method was removed. You should remove any calls to this method from your `SparkServiceProvider`.

### Updating Your `package.json` Dependencies

In your `package.json` file update the `laravel-mix` dependency to the latest version:

```
"laravel-mix": "^4.0.7"
```

Additionally, add the following dependencies:

```
"dinero.js": "^1.6.0",
"resolve-url-loader": "3.1.0",
"sass": "^1.22.5",
"sass-loader": "7.*",
"sweetalert2": "^8.13.6",
"vue-template-compiler": "^2.6.10"
```

Finally, remove the `sweetalert` dependency.

### Removing Calls To Removed Braintree Files

Remove the following lines from the `/resources/js/spark-components/bootstrap.js` file:

- `require('./auth/register-braintree');`
- `require('./settings/subscription/subscribe-braintree');`
- `require('./settings/payment-method-braintree');`
- `require('./settings/payment-method/update-payment-method-braintree');`

### Webpack Configuration

Replace the following line:

```
.copy('node_modules/sweetalert/dist/sweetalert.min.js', 'public/js/sweetalert.min.js')
```

With this:

```
copy('node_modules/sweetalert2/dist/sweetalert2.min.js', 'public/js/sweetalert.min.js')
```
