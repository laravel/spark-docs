# Server Customization Primer

- [Introduction](#introduction)
- [Model Classes](#model-classes)
- [Users](#users)
- [Teams](#team)
- [Views](#views)

## Introduction

Spark allows you to customize certain aspects of the system to better suit your needs. For example, you may wish to tweak how the current user is retrieved from the database, or how two-factor authentication is implemented.

When customizing the backend, you will typically use the `Spark::swap` method, which will allow you to swap out method implementations of various Spark core classes. Generally, you should rarely be required to extend a Spark class directly.

<a name="model-classes"></a>
## Model Classes

If you would like to customize the model names used by Spark from the `App` namespace to another custom namespace, you may use the `useUserModel` and `useTeamModel` methods in the `register` method of your `SparkServiceProvider`. You may need to define the `register` method if it does not already exist:

    Spark::useUserModel('Namespace\User');

    Spark::useTeamModel('Namespace\Team');

<a name="users"></a>
## Users

#### Current User

You may wish to customize how the current user is retrieved and sent to your front-end JavaScript application. For example, you may wish to load extra relationships on the `User` instance before it is returned. You may do so by swapping the `UserRepository@current` method:

    Spark::swap('UserRepository@current', function () {
        // Return the current user...
    });

If you are using Vue, the currently authenticated user is available to your application's JavaScript via `Spark.state.user`.

<a name="teams"></a>
## Teams

#### Finding Individual Teams

The `Laravel\Spark\Contracts\Repositories\TeamRepository` interface contains several methods you may wish to customize. To customize how individual teams are retrieved from the database, you may swap the `find` method:

    Spark::swap('TeamRepository@find', function ($id) {
        // Return the team with the given ID...
    });

If you are using Vue, the currently authenticated user's current team is available to your application's JavaScript via `Spark.state.currentTeam`.

#### Finding All Teams For A Given User

To customize how all of the teams for a given user are retrieved, you may swap the `forUser` method of the `TeamRepository`:

    Spark::swap('TeamRepository@forUser', function ($user) {
        // Return all of the given user's teams...
    });

If you are using Vue, All of the teams for the currently authenticated user are available to your application's JavaScript via `Spark.state.teams`.

<a name="views"></a>
## Views

When you create a new application using `spark new`, all of the Spark views are automatically exported to your application's `resources/views/vendor/spark` directory. You may customize any of these views as needed.

> **Note:** When you upgrade your application using `php artisan spark:update`, any views that have been modified will not be updated.
