# Available Variables

- [Introduction](#introduction)
- [Accessing Via Vue Properties](#accessing-via-vue-properties)
- [Accessing Via Global Spark Object](#accessing-via-global-spark-object)
- [The Spark Computed Property](#the-spark-computed-property)
    - [Adding Custom Properties](#adding-custom-properties)

<a name="introduction"></a>
## Introduction

While building your Spark application using Vue.js, you will commonly need access to a few pieces of key information such as the current user, the user's teams, and the user's current team. Spark makes it a breeze to access this information either using Vue component properties or the global `Spark.state` object.

<a name="accessing-via-vue-properties"></a>
## Accessing Via Vue Properties

You may ask for the `user`, `teams`, or `currentTeam` properties in your Vue components. For example, let's add the properties to the `home.js` component:

    Vue.component('home', {
        props: ['user', 'teams', 'currentTeam'],

        mounted() {
            //
        }
    });

Remember, a user may not have a current team if they do not belong to any teams at all. In this case, `currentTeam` will be `null` and the `teams` property will be an empty array.

Next, just pass the properties into your component in your `home.blade.php` file:

    <home :user="user" :teams="teams" :current-team="currentTeam" inline-template>

<a name="accessing-via-global-spark-object"></a>
## Accessing Via Global Spark Object

You may also access data using the `Spark.state` object. For example, let's do this in the `home.js` component. Typically you should pass this data down your application via properties; however, the `Spark.state` object can be useful if you have deeply nested components where it is inconvenient to pass the data as properties:

    Vue.component('home', {
        props: ['user'],

        mounted() {
            console.log(Spark.state.user);
            console.log(Spark.state.teams);
            console.log(Spark.state.currentTeam);
        }
    });

<a name="the-spark-computed-property"></a>
## The Spark Computed Property

Spark registers a global Vue mixin that makes a few properties available to all of your components. One of these properties is `spark`, which provides you with a variety of information about the current Spark application. By default, the `spark` computed property object contains the following information:

Property  | Description
------------- | -------------
`cardUpFront`  |  Indicates if the application requires credit cards up front.
`collectsBillingAddress`  |  Indicates if the application collects billing addresses.
`collectsEuropeanVat`  |  Indicates if the application collects European VAT.
`csrfToken`  |  The current Laravel CSRF token.
`currencySymbol`  |  The symbol used throughout the application for formatting currency.
`env`  |  The current Laravel application environment.
`roles`  |  All of the available user roles.
`userId`  |  The current user's ID.
`usesApi`  |  Indicates if the application exposes an API.
`usesTeams`  |  Indicates if the application supports teams.

You may access these properties in your templates or in your JavaScript:

    <!-- From Template -->
    <div v-if="spark.usesApi">
        This application has an API.
    </div>

    <!-- From JavaScript -->
    if (this.spark.usesApi) {
        console.log('This application has an API.');
    }

<a name="adding-custom-properties"></a>
### Adding Custom Properties

You can add your own properties to the `spark` computed property by adding them to your `resources/views/vendor/spark/layouts/app.blade.php` file. They will be merged with the default properties that are available on the object:

    window.Spark = <?php echo json_encode(array_merge(
        Spark::scriptVariables(), [
            'custom' => 'value'
            'another-custom-value' => 'value'
        ]
    )); ?>
