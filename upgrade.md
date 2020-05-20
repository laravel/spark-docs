# Upgrade Guide

- [Upgrading To Spark 10.0](#upgrade-spark-10.0)

<a name="upgrade-spark-9.0"></a>
## Upgrading To Spark 10.0

Spark 10.0 provides compatibility with Cashier 11. Cashier 11 includes support for multi-plan subscriptions.

### Upgrading Via Spark CLI

If you installed Spark via the `spark` CLI tool, you may run the `spark:update` Artisan command:

    php artisan spark:update --major

### Upgrading Via Composer

If you installed Spark via Composer, you may simply update your dependency version in your `composer.json` file and run the `composer update` command. Of course, in order for your GitHub user to access the repository, you should first join this repository in the Spark dashboard:

    "laravel/spark-aurelius": "~10.0"

### Cashier Upgrade Guide

After updating your Spark application, please review and make the necessary changes for Cashier 11.0 based on the [Cashier upgrade guide](https://github.com/laravel/cashier/blob/11.x/UPGRADE.md).


