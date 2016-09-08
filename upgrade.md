# Upgrade Guide

- [Upgrading To Spark 2.0](#upgrade-spark-2.0)

<a name="upgrade-spark-2.0"></a>
## Upgrading To Spark 2.0

Spark 2.0 is a free upgrade for your Spark applications and provides compatibility with Laravel 5.3.

Before upgrading to Spark 2.0 you should review the entire [Laravel 5.3 upgrade guide](https://laravel.com/docs/5.3/upgrade) and make any applicable changes to your application. There are no further code upgrades you need to make before using Spark 2.0 other than those documented in the Laravel upgrade guide. Once you have completed applying the changes listed in the Laravel 5.3 upgrade guide, you are ready to upgrade your underlying Spark dependency.

#### Via Spark CLI

If you installed Spark via the `spark` CLI tool, you may run the `spark:update` Artisan command:

    php artisan spark:update

> If you installed Spark via the `spark` CLI tool, you should consider switching to a Composer based Spark installation, which provides a more fine-tuned approach to managing Spark versions.

#### Via Composer

If you installed Spark via Composer, you may simply upgrade your dependency in your `composer.json` file and run the `composer update` command:

    "laravel/spark": "~2.0"

> You should also make sure to update your Cashier dependencies to `~7.0` for Stripe or `~2.0` if you are using Braintree.
