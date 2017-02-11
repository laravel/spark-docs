# Quickstart

- [Introduction](#introduction)
- [Installing Spark](#installing-spark)
- [Configuring Billing Plans](#configuring-billing-plans)
- [Building Your Application](#building-your-application)
- [Demo Application](#demo-application)


<a name="introduction"></a>
## Introduction

Spark provides all of the scaffolding you need to get started developing your next big idea. Authentication, API tokens and authentication, subscription billing, payment method updates, invoicing, password reset, and more is included out of the box.

In this guide, we'll walk through installing Spark and writing the first view for your application.

<a name="installing-spark"></a>
## Installing Spark

To install Spark, follow the instructions in the [installation guide](/docs/4.0/installation).

#### Post Installation Setup

Once Spark is installed, make sure the NPM dependencies have been installed via the `npm install` command, the `npm run dev` command has been executed, and the database migrations have ran. You should also configure your e-mail address as a "developer" so that you have access to the [Kiosk](/docs/4.0/kiosk). Simply add your e-mail address to the `$developers` property in the `App\Providers\SparkServiceProvider`.

> **Note:** If you need a development environment for your Spark project, be sure to check out [Homestead](https://laravel.com/docs/homestead).

<a name="configuring-billing-plans"></a>
## Configuring Billing Plans

Once your Spark project has been created, take a look at the `App\Providers\SparkServiceProvider` class. The `booted` method of this class is used to define your application's subscription plans. By default, one free plan and one paid plan is defined. You should change the ID of the paid plan from `provider-id-1` to match the ID of one of your plans that is actually defined in Stripe or Braintree.

> **Note:** Spark does not automatically define plans within Stripe / Braintee. You must define those manually on the provider of your choice.

By default, Spark is configured to require no credit-card up front and to grant new users a free trial period of 10 days. This configuration should work perfectly for the majority of applications. For more information on configuring billing plans, check out the [billing documentation](/docs/4.0/billing).

<a name="building-your-application"></a>
## Building Your Application

Once Spark has been installed and your billing plans are configured, you're ready to start building your application. By default, Spark installs a `/home` route in your `routes\web.php` file which displays the `home` Blade template. You are free to edit the `home.blade.php` template as needed to build your application.

The `home.blade.php` file also represents the `home` [Vue component](https://vuejs.org), which is defined at `resources/assets/js/components/home.js`. You may add your own JavaScript code to this component as needed. Of course, if you change this code, don't forget to run `gulp` to re-compile your application's JavaScript.

Of course, at this point, you may simply build your Laravel application however you wish. Spark has already installed scaffolding for all of the subscription management, authentication, and other settings views. Now  it's time for you to build your dream!

### Read The Documentation

For more information on using Spark, check out the rest of this documentation, which includes information on [back-end customization](/docs/4.0/customization) and [front-end customization](/docs/4.0/client-customization), as well as documentation pages covering many other areas of Spark. **It's very important that you read through this documentation in its entirety.**

<a name="demo-application"></a>
## Demo Application

You may download a sample to-do list application built on Spark to learn more about building Spark applications. This demo application can be used to get a basic idea of how to get started building your own application after installing and configuring Spark. The to-do list contains a basic task list and an activity log of completed tasks.

<div markdown="0">
<a href="/sample/download">
<button class="btn btn-info" style="margin-top: 20px;">
    <i class="fa fa-btn fa-cloud-download"></i>Download Sample Application
</button>
</a>
</div>

**In order to download the demo application, you must have a valid Spark license.**
