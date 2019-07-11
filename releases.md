# Release Notes

- [8.0.0](#8.0.0)
- [7.0.0](#7.0.0)
- [6.0.0](#6.0.0)
- [5.0.0](#5.0.0)
- [4.0.0](#4.0.0)
- [3.0.0](#3.0.0)
- [2.0.0](#2.0.0)
- [1.0.0](#1.0.0)

<a name="8.0.0"></a>
## 8.0.0

The 8.0 release of Spark provides Laravel 5.8 compatibility.

Spark 8.0 is a simple maintenance release to provide compatibility with Laravel 5.8 and Cashier 9.0. As such, there are no Spark specific upgrade instructions. You should simply upgrade your application using the [Laravel 5.8 upgrade guide](https://laravel.com/docs/5.8/upgrade). In addition, ensure that your application's `composer.json` file is requiring Laravel Cashier `^9.0`.

<a name="7.0.0"></a>
## 7.0.0

The 7.0 release of Spark provides Laravel 5.7 compatibility.

<a name="6.0.0"></a>
## 6.0.0

The 6.0 release of Spark provides Laravel 5.6 compatibility. In addition, Spark 6.0 introduces Bootstrap 4, localization, per-seat pricing, Stripe Elements, and UI enhancements.

<a name="4.0.0"></a>
## 4.0.0

The 4.0 release of Spark upgraded the front-end HTTP library to Axios. In addition, new Spark 4.0 applications are configured to use [Laravel Mix](https://laravel.com/docs/mix) and Webpack instead of Laravel Elixir. To learn more about upgrading your application to Spark 4.0, please consult the [upgrade guide](/docs/4.0/upgrade).

<a name="3.0.0"></a>
## 3.0.0

The 3.0 release of Spark upgraded the front-end Vue.js dependencies to Vue 2.0. To learn more about upgrading your application to Spark 3.0, please consult the [upgrade guide](/docs/3.0/upgrade).

<a name="2.0.0"></a>
## 2.0.0

The 2.0 release of Spark provides compatibility with Laravel 5.3.

For the 2.0 release, the Spark installer has been deprecated in favor of simply [installing Spark via Composer](/docs/3.0/installation).

<a name="1.0.0"></a>
## 1.0.0

The initial release of Laravel Spark. Spark provides instant scaffolding for subscription billing applications including authentication, password reset, billing plans, payment method updates, invoices, coupons, user impersonation, and more with support for Stripe and Braintree.
