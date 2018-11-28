# Upgrade Guide

- [Upgrading To Spark 7.1](#upgrade-spark-7.1)
- [Upgrading To Spark 7.0](#upgrade-spark-7.0)

<a name="upgrade-spark-7.1"></a>
## Upgrading To Spark 7.1

Spark 7.1 introduces the email verification out of the box. To prepare your application for the upgrade you need to add the following database field to your users' table.

    $table->timestamp('email_verified_at')->nullable();

<a name="upgrade-spark-7.0"></a>
## Upgrading To Spark 7.0

Spark 7.0 is a simple maintenance release to provide compatibility with Laravel 5.7. As such, there are no Spark specific upgrade instructions. You should simply upgrade your application using the [Laravel 5.7 upgrade guide](https://laravel.com/docs/5.7/upgrade).
