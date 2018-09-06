# Quickstart

- [Introduction](#introduction)
- [Installing Spark](#installing-spark)
- [Configuring Billing Plans](#configuring-billing-plans)
- [Building Your Application](#building-your-application)


<a name="introduction"></a>
## Introduction

Spark provides all of the scaffolding you need to get started developing your next big idea. Authentication, API tokens and authentication, subscription billing, payment method updates, invoicing, password reset, and more is included out of the box.

In this guide, we'll walk through installing Spark and writing the first view for your application.

<a name="installing-spark"></a>
## Installing Spark

To install Spark, follow the instructions in the [installation guide](/docs/7.0/installation).

#### Post Installation Setup

Once Spark is installed, make sure the NPM dependencies have been installed via the `npm install` command, the `npm run dev` command has been executed, and the database migrations have run. You should also configure your e-mail address as a "developer" so that you have access to the [Kiosk](/docs/7.0/kiosk). To do this, add your e-mail address to the `$developers` property in the `App\Providers\SparkServiceProvider`.

<a name="configuring-billing-plans"></a>
## Configuring Billing Plans

Once your Spark project has been created, take a look at the `App\Providers\SparkServiceProvider` class. The `booted` method of this class is used to define your application's subscription plans. By default, one free plan and one paid plan is defined. You should change the ID of the paid plan from `provider-id-1` to match the ID of one of your plans that is actually defined in Stripe or Braintree.

> **Note:** Spark does not automatically define plans within Stripe / Braintree. You must define those manually on the provider of your choice.

By default, Spark is configured to require no credit-card up front and to grant new users a free trial period of 10 days. This configuration should work perfectly for the majority of applications. For more information on configuring billing plans, check out the [billing documentation](/docs/7.0/billing).

<a name="building-your-application"></a>
## Building Your Application

Once Spark has been installed and your billing plans are configured, you're ready to start building your application. By default, Spark installs a `/home` route in your `routes/web.php` file which displays the `home` Blade template. You are free to edit the `home.blade.php` template as needed to build your application.

The `home.blade.php` file also represents the `home` [Vue component](https://vuejs.org), which is defined at `resources/assets/js/components/home.js`. You may add your own JavaScript code to this component as needed. Of course, if you change this code, don't forget to run `npm run dev` to re-compile your application's JavaScript.

Of course, at this point, you may simply build your Laravel application however you wish. Spark has already installed scaffolding for all of the subscription management, authentication, and other settings views. Now  it's time for you to build your dream!

### Read The Documentation

For more information on using Spark, check out the rest of this documentation, which includes information on [back-end customization](/docs/7.0/customization) and [front-end customization](/docs/7.0/client-customization), as well as documentation pages covering many other areas of Spark. **It's very important that you read through this documentation in its entirety.**
