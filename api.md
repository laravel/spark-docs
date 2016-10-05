# API

- [Introduction](#introduction)
- [Configuration](#configuration)
- [Abilities](#abilities)
- [Routes](#routes)
- [API Driven Applications](#api-driven-applications)
    - [Sharing Your API With Your JavaScript Application](#sharing-your-api)
- [Using Passport](#using-passport)

<a name="introduction"></a>
## Introduction

Spark makes it simple for your users to generate API tokens for your application. You may even allow users to grant abilities to tokens to limit the scope of actions a given token can perform.

In addition, Spark ships with a custom Laravel authentication guard to authenticate requests that are made using these tokens, allowing you to easily share the same back-end API for your main web application and your API SDKs you provide to your users and third parties.

<a name="configuration"></a>
## Configuration

To enable API support, make sure that the `$usesApi` property of your `SparkServiceProvider` is set to `true`. When API support is enabled, an "API" tab will be available to your users via their settings dashboard. From this dashboard users may generate and revoke API tokens.

<a name="abilities"></a>
## Abilities

If you would like to give users the ability to grant "abilities" to tokens and constrain the actions that a given token can perform, you may use the `tokensCan` method on the `Spark` configuration object. You should place this call within the `booted` method of your `SparkServiceProvider`:

    Spark::tokensCan([
        'read-servers' => 'Read Servers',
        'create-servers' => 'Create Servers',
        'delete-servers' => 'Delete Servers',
    ]);

The keys of the given array should be unique "slugs" that represent the ability while the values are the displayable, human-readable versions of the abilities. Once you have defined these abilities, they will automatically be displayed on the API settings dashboard.

### Checking Abilities On Active Tokens

Of course, you need an easy method of determining if a token authenticated user's token can perform a given action. Spark makes it a cinch. When a user is authenticated via the `api` guard, you may access the `token` property on the user instance. The `token` property contains a `can` method which can be used to verify if a token has a given ability:

    Route::get('/api/servers', ['middleware' => 'auth:api', function () {
        if (Auth::user()->token()->can('read-servers')) {
            //
        }
    }]);

<a name="routes"></a>
## Routes

Spark automatically generates a `app/Http/api.php` file to contain all of your API routes. This file is automatically loaded by the `RouteServiceProvider` and that provider also applies the `auth:api` middleware by default. You are free to modify these settings as necessary for your own application. However, the default configuration should be suitable for most applications.

<a name="api-driven-applications"></a>
## API Driven Applications

Once a user has an API token, they can access your API by passing the token in an `api_token` query string parameter when making requests. You should assign the `auth:api` middleware to all of your API routes you wish to authenticate:

    Route::get('/api/users', ['middleware' => 'auth:api', function () {
        //
    ]);

<a name="sharing-your-api"></a>
### Sharing Your API With Your JavaScript Application

> **Note:** This feature requires that you use [Vue.js](https://vuejs.org) as your application's JavaScript framework.

If your application exposes an API, it can be beneficial to consume that API yourself from your application's front-end JavaScript code. This allows you to share the same API between your application and your API SDKs you may be shipping on various package managers.

Spark makes it entirely painless to consume your API in this way. Simply make requests to your API routes using your JavaScript framework as normal. You do not need to pass any token or credentials. All of the authentication will be handled automatically by Spark, which generates "transient", short-lived API tokens behind the scenes automatically when users load your application's pages. These API tokens are automatically refreshed in the background by Spark.

So, if you are using Vue, you may simply call your API routes like normal. No additional configuration is necessary to start consuming your API:

    this.$http.get('/api/users')
        .then(response => {
            this.users = response.data;
        });


<a name="using-passport"></a>
## Using Passport

In addition to the built-in API authentication driver, Spark supports [Laravel Passport](https://laravel.com/docs/5.3/passport) out of the box. You may install Passport by following the [Passport installation documentation](https://laravel.com/docs/5.3/passport).

After installing Passport, you can almost always use Passport as you would in any other Laravel application. However, instead of calling the `Passport::tokensCan()` method, you should use the `Spark::tokensCan()` method.
